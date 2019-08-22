# Set Up Network and HTTP Load Balancers

## Network Load Balancer

ref : https://cloud.google.com/load-balancing/docs/network/

regional and non-proxied.

VPC 네트워크에 있는 같은 리전의 VM들의 트래픽을 분배해준다.

target pool : forwarding rule 에 정의된 traffic을 받을 수 있는 instance group 이다.

target pool 생성
```
gcloud compute target-pools create nginx-pool
```

instance group 생성
```
gcloud compute instance-groups managed create nginx-group \
         --base-instance-name nginx \
         --size 2 \
         --template nginx-template \
         --target-pool nginx-pool
```

forwarding rule 생성
```
gcloud compute forwarding-rules create nginx-lb \
         --region us-central1 \
         --ports=80 \
         --target-pool nginx-pool
```

## Http Load Balancer

HTTP(S) 에 대한 global Load Balancer 이다. 

1. forwarding rule 은 다이렉트로 request 를 target proxy 로 전달한다.
2. target proxy 는 url map 을 통해서 적절한 backend service 로 라우팅 해준다.
3. backend service 는 request 를 서버 용량, 상태에 따라서 적절한 backend instance 로 전달한다. 각각의 backend instance는 health check 를 사용한다. 

heath check  생성
```
gcloud compute http-health-checks create http-basic-check
```

instance group 생성
```
gcloud compute instance-groups managed create nginx-group \
         --base-instance-name nginx \
         --size 2 \
         --template nginx-template \
         --target-pool nginx-pool
```
```
gcloud compute instance-groups managed \
       set-named-ports nginx-group \
       --named-ports http:80
```
nginx-group 이라는 instance-group 의 80 포트에 http를 할당한다.

backend service 생성
```
gcloud compute backend-services create nginx-backend \
      --protocol HTTP --http-health-checks http-basic-check --global
```

backend service 에 instance group 추가
```
gcloud compute backend-services add-backend nginx-backend \
    --instance-group nginx-group \
    --instance-group-zone us-central1-a \
    --global
```

default URL map 생성
```
gcloud compute url-maps create web-map \
    --default-service nginx-backend
```

target proxy 생성
```
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map
```

forwarding-rule 생성
```
gcloud compute forwarding-rules create http-content-rule \
        --global \
        --target-http-proxy http-lb-proxy \
        --ports 80
```

https://cloud.google.com/load-balancing/docs/https/
https://google.qwiklabs.com/focuses/558?parent=catalog
https://cloud.google.com/sdk/gcloud/reference/compute/instance-groups/set-named-ports