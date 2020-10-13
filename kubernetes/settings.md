# K8S 설치하기

1. Virtual BOX 환경설정
    - 네트워크 
        - 네트워크 이름 : k8s-network
        - 네트워크 CIDR : 10.0.1.0/24
        - 네트워크 옵션 : DHCP 지원 체크

2. Machine 설정

    - 일반 : 고급
        - 클립보드 공유 : 양방향
        - 드래그 앤 드롭 : 양방향
    - 시스템 : 프로세서 
        - 개수 : 2
    - 네트워크 : 어댑터 1
        - 다음에 연결됨 : Nat 네트워크
        - 이름 : k8s-network  (미리 만들어야된다.)
    - 네트워크 : 어댑터 2
        - 다음에 연결됨 : Nat    
    - 공유폴더 **(Optional)**
        - 폴더 설정, 마운트 지정

3. 설치

4. 완료후 설정 **(Optional)**
    - 장치 : 게스트 확장 CD 삽입 -> 설치

5. Ubuntu 설정 
    1. 관리자 계정으로
        ```
        sudo -i
        ```        
    2.  /env/environment 설정 **(Optional)**
        ```
        export http_proxy=http://[IP]:[PORT]
        export https_proxy=http://[IP]:[PORT]
        export no_proxy=
        ```

    3. 가상메모리 미사용
        ```
        swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
        ```

    4. 인증서 설정 **(Optional)**
    
        /usr/local/share/ca-certificates/ 에 인증서 복사

        update-ca-certificates 실행

    5. 도커 설치
        ```
        apt update
        apt install -y docker.io
        cat << EOF > /etc/docker/daemon.json
        {
            "exec-opts": ["native.cgroupdriver=systemd"]
        }
        EOF
        ```
    6. 프록시 추가 **(Optional)**
        ```
        vi /lib/systemd/system/docker.service

        [Service]
        Environment="HTTP_PROXY=http://[IP]:[PORT]/" "HTTPS_PROXY=http://[IP]:[PORT]/" "NO_PROXY=localhost,127.0.0.1,10.0.1.7"
        ```
    7. 도커 재기동
        ```
        systemctl daemon-reload
        systemctl restart docker
        ```

    8. K8S 패키지 매니저 레파지토리 추가
        ```
        curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg -k | apt-key add -
        cat << EOF > /etc/apt/sources.list.d/kubernetes.list
        deb http://apt.kubernetes.io/ kubernetes-xenial main
        EOF
        ```
    9. K8S 설치
        ```
        apt update -y
        apt-get update
        apt-get install -y kubelet kubeadm kubectl
        ```    

        1. Master Node 설정
            1. kubeadm init
                ```
                kubeadm init --pod-network-cidr=10.244.0.0/16
                ```
                설치후 아래와 같이 메세지가 나오면 정상적으로 설치 완료
                ```
                To start using your cluster, you need to run the following as a regular user:

                mkdir -p $HOME/.kube
                sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
                sudo chown $(id -u):$(id -g) $HOME/.kube/config

                You should now deploy a pod network to the cluster.
                Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
                https://kubernetes.io/docs/concepts/cluster-administration/addons/

                Then you can join any number of worker nodes by running the following on each as root:

                kubeadm join 10.0.1.7:6443 --token bfq4lq.1k5qiesq8norkwck \
                    --discovery-token-ca-cert-hash sha256:66249ed983b26844267631ae0a061c7d02386941e154341bc669c72add72afeb             
                ```
            2. kubectl 구성
                ```
                mkdir -p $HOME/.kube
                sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
                sudo chown $(id -u):$(id -g) $HOME/.kube/config
                ``` 
            3. pod network 플러그인 설치
                ```
                kubectl apply -f http://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
                ```
        2. Worker Node 설정

            ```
            kubeadm join 10.0.1.7:6443 --token bfq4lq.1k5qiesq8norkwck \
                --discovery-token-ca-cert-hash sha256:66249ed983b26844267631ae0a061c7d02386941e154341bc669c72add72afeb    
            ```



## K8S 설치시 Trouble Shooting

- kubeadm init 했는데 다음과 같이 나오는 경우

```
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.

Unfortunately, an error has occurred:
        timed out waiting for the condition
This error is likely caused by:
        - The kubelet is not running
        - The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled)
If you are on a systemd-powered system, you can try to troubleshoot the error with the following commands:
        - 'systemctl status kubelet'
        - 'journalctl -xeu kubelet'

Additionally, a control plane component may have crashed or exited when started by the container runtime.
To troubleshoot, list all containers using your preferred container runtimes CLI, e.g. docker.
Here is one example how you may list all Kubernetes containers running in docker:
        - 'docker ps -a | grep kube | grep -v pause'
        Once you have found the failing container, you can inspect its logs with:
        - 'docker logs CONTAINERID'
error execution phase wait-control-plane: couldn't initialize a Kubernetes cluster
```
https://www.oops4u.com/2360

참조한 사이트에서 처럼 docker 와 k8s 의 cgroup 드라이버가 달라서 일 경우는 참조 사이트 처럼 해결하면 된다.

하지만 나같은 경우는 위 해결 방법으로도 해결이 안되었다. 

- 'journalctl -xeu kubelet' 

이 명령어를 실행 하면 아래와 같이 로그가 남았다. 
```
Apr 30 22:19:38 master kubelet: W0430 22:19:38.226441    2372 cni.go:157] Unable to update cni config: No networks found in /etc/cni/net.d
Apr 30 22:19:38 master kubelet: E0430 22:19:38.226587    2372 kubelet.go:2067] Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
```

내 경우에는 위에 2.  /env/environment 설정 에 보면 **no_proxy** 항목에 현재 K8S 가 설치된 Machine 의 IP 를 추가했더니 해결이 되었다.



To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.0.1.7:6443 --token bfq4lq.1k5qiesq8norkwck \
    --discovery-token-ca-cert-hash sha256:66249ed983b26844267631ae0a061c7d02386941e154341bc669c72add72afeb 


- kubeadm join 했는데 다음과 같이 나올 경우
```
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileAvailable--etc-kubernetes-kubelet.conf]: /etc/kubernetes/kubelet.conf already exists
	[ERROR Port-10250]: Port 10250 is in use
	[ERROR FileAvailable--etc-kubernetes-pki-ca.crt]: /etc/kubernetes/pki/ca.crt already exists
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```
    VM 에서 다음과 같이 kubeadm join 을 했는데 에러가 났을경우 (node가 2개인데 2번째 노드 구성시)kubeadm reset 을 한번 실행하고 다시 join 명령어를 실행해준다.