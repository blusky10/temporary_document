# Networking 102

## Projects

Project 는 Network 를 포함하고 있며 Network 에는 Subnetwork, Firewall rules, Route 가 포함된다.

Organization > Folders > Projects > Resources

Network : 외부에서 각각의 Resource 들을 직접 접속한다. 외부에서 들어오거나 외부로 나가는 접속에 대해서 Firewall 을 사용한다. Global(Multiple Region) 또는 Regional(Single Region) 이 가능하다.

Subnetwork : Compute Engine instance 같이 관련된 resouce 들을 group 화 한다. Regional 만 가능하며 auto mode 와 custom mode 가 있다.

1. auto mode : Network 생성시 region 당 하나의 subnet이 자동으로 생성된다.
2. custom mode : Network 생성시에는 subnet이 생성되지 않는다. subnet 이 region 당 존재하지 않을 수 있고 여러개 존재할 수 있다.


