# Networking 102

## Projects

Project 는 Network 를 포함하고 있며 Network 에는 Subnetwork, Firewall rules, Route 가 포함된다.

Organization > Folders > Projects > Resources

## Network

Network : 외부에서 각각의 Resource 들을 직접 접속한다. 외부에서 들어오거나 외부로 나가는 접속에 대해서 Firewall 을 사용한다. Global(Multiple Region) 또는 Regional(Single Region) 이 가능하다. 프로젝트가 생성되면 default Network 가 생성된다.

Subnetwork : Compute Engine instance 같이 관련된 resouce 들을 group 화 한다. Regional 만 가능하며 auto mode 와 custom mode 가 있다.

1.auto mode : Network 생성시 region 당 하나의 subnet이 자동으로 생성된다.
2.custom mode : Network 생성시에는 subnet이 생성되지 않는다. subnet 이 region 당 존재하지 않을 수 있고 여러개 존재할 수 있다.

### Network 생성

1.auto

~~~java
gcloud compute networks create autoNetwork --subnet-mode=auto
~~~

2.custom

~~~java
gcloud compute networks create customNetwork --subnet-mode=custom
~~~

custom 일 경우 subnet이 자동으로 생성되지 않기 때문에 subnet 도 만들어줘야 한다.

~~~java
gcloud compute networks subnets create customNetwork --network=privatenet \
--region=us-central1 --range=10.0.0.0/24 --enable-private-ip-google-access
~~~

## Cloud Route

Instance 를 Nat gateway 로 활용할 수 있다.

~~~java
gcloud compute routes create NAME --destination-range=DESTINATION_RANGE (--next-hop-address=NEXT_HOP_ADDRESS     
| --next-hop-gateway=NEXT_HOP_GATEWAY     | --next-hop-instance=NEXT_HOP_INSTANCE     
| --next-hop-vpn-tunnel=NEXT_HOP_VPN_TUNNEL) [--description=DESCRIPTION] [--network=NETWORK; default="default"] [--next-hop-instance-zone=NEXT_HOP_INSTANCE_ZONE] [--next-hop-vpn-tunnel-region=NEXT_HOP_VPN_TUNNEL_REGION] [--priority=PRIORITY; default=1000] [--tags=TAG,[TAG,…]] [GCLOUD_WIDE_FLAG …]
~~~

ex)
gcloud compute routes create nat-route --network customNetwork \
--destination-range 0.0.0.0/0 --next-hop-instance privatenet-bastion \
--next-hop-instance-zone us-central1-c --tags nat-me --priority 800

--next-hop-instance : route 를 핸들링 할 instance 이름 (--next-hop-instance 을 사용할 경우 --next-hop-instance-zone 값도 필수로 넣어야 한다. )
--tags : route 를 적용할 instance 들의 모음

## Network 관련된 IAM Role

Network Admin

networks, subnetworks, addresses, routes등에 대한 create, modify, delete 권한 (제외 : firewall rule, SSL certificates - view)

Security Admin

firewall rule, SSL ceritifates 에 대한 create, modify 권한.

Network Viewer

읽기만 가능한 권한


참고자료
https://www.qwiklabs.com/focuses/556?parent=catalog
https://cloud.google.com/sdk/gcloud/reference/compute/routes/create