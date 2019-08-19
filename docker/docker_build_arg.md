# Docker build 할때 parameter 처리 방법

Spring boot Application 을 Dockerfile 로 build 할 때에 profile 값을 전달 해야 할 경우가 있다.

다음과 같은 방법이 있다. 

1.Dockerfile 에 profile 값을 넣고 build 를 한다.

~~~java
FROM java:8
ADD target/app.jar app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=dev","-jar","/app.jar"]
~~~

2.Docker run 할때 환경 변수로 전달한다.

~~~java
docker run -d -p 8080:8080 -e "SPRING_PROFILES_ACTIVE=dev" --name rest-api dockerImage:lates
~~~

3.DockerCompose 로 전달한다.

~~~yaml
version: "3"
services:
  rest-api:
     image: rest-api:0.0.1
     ports:
       - "8080:8080"
     environment:
       - "SPRING_PROFILES_ACTIVE=dev"  
~~~

여기에서 난 1번을 선택해서 적용을 했는데 한가지 Dockerfile 에 전달되는 profile 값이 변경 될수 있도록 하고 싶었다. 그럴려면 docker build 시점에 값을 넘겨줘야 하는데 document를 찾아보니 ARG 를 사용하게 되면 docker build 시점에 값을 넘길수가 있다.

Dockerfile
~~~
FROM ubuntu
VOLUME /tmp
ADD app.jar app.jar
RUN bash -c 'touch ./app.jar'
ARG SPRING_PROFILES_ACTIVE
RUN echo $SPRING_PROFILES_ACTIVE
ENV SPRING_PROFILES_ACTIVE=$SPRING_PROFILES_ACTIVE
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom", "-jar","./app.jar"]
~~~

~~~
docker build --build-arg SPRING_PROFILES_ACTIVE=dev .
~~~

결과적으로 docker build 시에 --build-arg 로 SPRING_PROFILES_ACTIVE 값을 넘겨주고 Dockerfile 에서는 전달 받은 SPRING_PROFILES_ACTIVE 값을 ENV 로 등록을하면 ENTRYPOINT 에 -D 옵션을 넣어서 실행하지 않아도 자동으로 profile 이 적용되서 실행이 된다.

https://docs.docker.com/engine/reference/builder/#arg
https://stackoverflow.com/questions/43707770/spring-boot-in-docker       