# docker container

## 1. 컨테이너를 학습해야하는 이유&#x20;

* 어플리케이션이 배포되는 환경을 서버에 직접 설치하여 구성할 경우, [snowflake server 이슈](https://bcho.tistory.com/1224)에 직면한다.
* 서비스는 점차 확장되고, 기존에 사용하던 서버와 새로 구축한 서버 간의 설정 차이가 발생하게 되고, 관리자의 인적 요소에도 영향을 받게 된다.&#x20;
* 기존에는 이를 해결하기 위해서 스크립트를 통한 자동화 방식, kickstart 등을 거쳐서 OS 가상화 방식까지 다양한 형태로 서버를 관리해왔다.&#x20;

### 1-1. 기존의 OS 가상화 방식&#x20;

* 기존의 OS 가상화 기술은 하이퍼바이저 기술을 이용해서 여러개의 운영체제를 하나의 호스트에서 생성해 사용하는 방식이다.&#x20;
  * 하이버바이저 : 호스트 컴퓨터에서 다수의 운영체제를 동시에 실행하기 위한 논리적 플랫폼&#x20;
* 각종 시스템 자원을 가상화하고, 독립된 공간을 생성하는 작업은 반드시 하이퍼바이저를 거치기 때문에 일반 호스트에 비해서 성능 손실이 발생하게 된다.&#x20;
* 가상머신은 guest OS 를 사용하기 위한 라이브러리, 커널 등을 전부 포함하기 때문에 가상머신을 배포하기 위한 이미지로 만들었을 때 이미지 크기가 너무 커지게 되어서 이미지를 어플리케이션으로 배포하기에는 무리가 있었다.&#x20;
  * [전가상화와 반가상화](https://m.blog.naver.com/complusblog/220990379931)

### 1-2. 컨테이너의 등장&#x20;

* 단순히 어플리케이션을 띄우고 싶었던 것 뿐인데, OS 까지 띄워야하는 것은 너무 낭비이다.&#x20;
* 격리된 CPU, memory, disk, network 를 가진 공간을 만들고, 이 공간에서 프로세스를 실행해서 유저에게 서비스 하려면 어떻게 해야할까? 에 대한 고민부터 시작&#x20;
* 컨테이너에 필요한 커널은 호스트의 커널을 공유해서 사용하고, 컨테이너 안에는 어플리케이션을 구동하는데 필요한 라이브러리 및 실행파일만 존재하도록 한다.&#x20;
* 이렇게 되면, 이미지로 만들었을 때, 이미지의 용량은 가상머신에 비해 훨씬 줄어들게 된다.&#x20;
* 컨테이너의 내용을 수정해도 호스트 OS 에 영향을 전혀 미치지 않는다.&#x20;
* 어플리케이션의 개발과 배포가 편해진다.&#x20;
* 여러 어플리케이션의 독립성과 확장성이 높아진다.&#x20;

## 2. 컨테이너의 프로세스 추상화&#x20;

### 2-1. 도커 설치&#x20;

```sh
$ sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo apt-key fingerprint 0EBFCD88 && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install -y docker-ce && \
sudo usermod -aG docker ubuntu && \
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose && \
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

```

### 2-2. 도커 데몬&#x20;

* 사용자가 명령어를 입력하면, docker.sock 을 통해서 도커 데몬의 API 를 호출한다.&#x20;
* 도커 데몬에서 발생하는 이벤트는 `docker events` 를 통해서 확인 가능하다.&#x20;
* 추가적으로, `docker stats` 와 `docker system df` 명령어를 통해서 도커가 현재 host 의 자원을 얼마나 사용하고 있는지 확인할 수 있다.&#x20;

```sh
## docker 명령어가 dockerd라는 도커 데몬과 docker.sock을 참조하고 있음을 확인할 수 있습니다.
$ sudo lsof -c docker
dockerd     880 root  txt       REG              202,1 102066512      55737 /usr/bin/dockerd
dockerd     880 root    6u     unix 0xffff953085804400       0t0      18324 /var/run/docker.sock type=STREAM

$ sudo docker run -it ubuntu:latest bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
3ff22d22a855: Pull complete
e7cb79d19722: Pull complete
323d0d660b6a: Pull complete
b7f616834fd0: Pull complete
Digest: sha256:5d1d5407f353843ecf8b16524bc5565aa332e9e6a1297c73a92d3e754b8a636d
Status: Downloaded newer image for ubuntu:latest
root@b7345bbb0ec1:/#

$ sudo docker events
2020-08-16T05:47:10.885807752Z image pull ubuntu:latest (name=ubuntu)
2020-08-16T05:47:11.247030355Z container create b7345bbb0ec1fae4b183c0fe1947a70a3045ba8d75f74876a812a37c40921707 (image=ubuntu:latest, name=awesome_ishizaka)
2020-08-16T05:47:11.249065810Z container attach b7345bbb0ec1fae4b183c0fe1947a70a3045ba8d75f74876a812a37c40921707 (image=ubuntu:latest, name=awesome_ishizaka)
2020-08-16T05:47:11.278151263Z network connect 6b6ce553a425c9392c5a65b8dcd2a57e1665289354b97f430758b745b1dc86a7 (container=b7345bbb0ec1fae4b183c0fe1947a70a3045ba8d75f74876a812a37c40921707, name=bridge, type=bridge)
2020-08-16T05:47:11.529841638Z container start b7345bbb0ec1fae4b183c0fe1947a70a3045ba8d75f74876a812a37c40921707 (image=ubuntu:latest, name=awesome_ishizaka)
2020-08-16T05:47:11.531931004Z container resize b7345bbb0ec1fae4b183c0fe1947a70a3045ba8d75f74876a812a37c40921707 (height=25, image=ubuntu:latest, name=awesome_ishizaka, width=167)

```

### 2-3. 도커 컨테이너&#x20;

* 도커 컨테이너는 호스트 시스템의 커널을 공유한다.&#x20;

```sh
$ docker run -it ubuntu:latest bash
root@03d7ffae4c3c:/# uname -a
Linux 03d7ffae4c3c 5.3.0-1032-aws #34~18.04.2-Ubuntu SMP Fri Jul 24 10:06:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

$ docker run -it centos:latest bash
[root@6ec49524540c /]# uname -a
Linux 6ec49524540c 5.3.0-1032-aws #34~18.04.2-Ubuntu SMP Fri Jul 24 10:06:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

# 맥OS는 리눅스가 아니므로 네이티브하게 도커를 사용할 수 없어, linuxkit 기반 경량 가상머신 위에서 도커를 실행합니다.
# 이후의 실습은 리눅스에서 실행하길 권장합니다.
❯ docker run -it centos:latest bash
[root@c3fdb0cda9ac /]# uname -a
Linux c3fdb0cda9ac 4.19.76-linuxkit #1 SMP Tue May 26 11:42:35 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

```

* 컨테이너는 이미지에 따라서 실행되는 환경(파일 시스템)이 달라진다.&#x20;
* 이것이 가능한 이유는 chroot 를 활용하여 이미지(파일의 집합)를 루트 파일 시스템으로 강제로 인식시켜 프로세스를 실행하기 때문이다.&#x20;

```sh
$ docker run -it ubuntu:latest bash
root@03d7ffae4c3c:/# cat /etc/*-release
DISTRIB_ID=Ubuntu

$ docker run -it centos:latest bash
[root@6ec49524540c /]# cat /etc/*-release
CentOS Linux release 8.2.2004 (Core)

```

* 컨테이너도 결국은 프로세스이다.&#x20;
  * [참고](https://brainbackdoor.tistory.com/27)

```sh
## 우선, 터미널의 PID를 확인해봅니다.
$ echo $$
3441
$ sudo ps -ef | grep 3441
ubuntu    3441  3440  0 05:46 pts/2    00:00:00 -bash

## 그럼, PID 1의 Command를 확인해봅니다.
$ cat /proc/1/comm
systemd
$ pstree -p
systemd(1)─┬─accounts-daemon(849)─┬─{accounts-daemon}(859)
...

## 이제 nginx 컨테이너를 실행해봅니다.
$ docker run --name nginx -d -p 80:80 nginx:latest
$ docker exec -it nginx bash
root@acaf67af191e:/# echo $$
324

## nginx 컨테이너의 PID 1 의 Command는 nginx임을 확인할 수 있습니다. 
$ sudo docker exec -it acaf67af191e cat /proc/1/comm
nginx
root@acaf67af191e:/# apt-get update
root@acaf67af191e:/# apt-get install psmisc
root@acaf67af191e:/# pstree -p
nginx(1)--nginx(28)

## 하지만 사실은 PID 4453 프로세스임을 확인할 수 있습니다. 
## 서버에선 그저 nginx 프로세스가 하나 띄워져있다고 생각합니다.
$ cat /var/run/docker/runtime-runc/moby/acaf67af191e5165b98b427b851f9b29a1450d20abd4e2e5287463ccc02c24a0/state.json | jq '.init_process_pid'
4453

$ cat /var/run/docker/runtime-runc/moby/acaf67af191e5165b98b427b851f9b29a1450d20abd4e2e5287463ccc02c24a0/state.json | jq '.namespace_paths'
{
  "NEWCGROUP": "/proc/4453/ns/cgroup",
  "NEWIPC": "/proc/4453/ns/ipc",
  "NEWNET": "/proc/4453/ns/net",
  "NEWNS": "/proc/4453/ns/mnt",
  "NEWPID": "/proc/4453/ns/pid",
  "NEWUSER": "/proc/4453/ns/user",
  "NEWUTS": "/proc/4453/ns/uts"
}

$ ls -Al /proc/1/ns | awk '{ print $9 $10 $11 }'
$ ps -ef | grep 4453
$ ps -auxfg

```

* 컨테이너의 실행환경을 이해하면 옵션을 사용하기도 훨씬 수월해진다.&#x20;
  * \-i : 표준 입력을 받도록 설정하는 것. 파이프라이닝이나 키보드 입력 등을 말한다.&#x20;
  * \-t : 가상 터미널 설정이다.&#x20;
  * \-it 가 조합되면, 가상 터미널로 표준입력을 할 수 있으며, -t 하나만 쓰여서는 아무런 의미가 없다.&#x20;
    * 터미널은 있는데 입력은 안되는 상황이 되므로!&#x20;
    * \-i는 홀로 쓰여도 터미널 없이 동작하는 파이프라이닝 등으로 입력을 하는 기능에 쓰일 수 있다.&#x20;
  * \-d : -i, -t 와 함께 쓰일 수 없다. background 에서 돌아가기 때문에 -i 로 입력을 대기할수도, -t 로 가상터미널을 제공할수도 없기 때문!&#x20;
* 도커 데몬은 크게 컨테이너, 이미지 빌드, 관리, 공유, 실행, 및 컨테이너 인스턴스 관리 등의 기능을 한다.&#x20;
* 모든 컨테이너를 자식 프로세스로 소유한다.&#x20;
  * [흔들리는 도커(Docker)의 위상 - OCI와 CRI 중심으로 재편되는 컨테이너 생태계](https://www.samsungsds.com/kr/insights/docker.html)

## 3. 도커 이미지&#x20;

* 이미지 : 파일들의 집합. 컨테이너는 이 파일들의 집합 위에서 실행된 특별한 프로세스&#x20;
* 프로세스의 데이터가 변경되더라도 원본 프로그램 이미지를 변경할 수 없듯이, 컨테이너의 데이터가 변경되더라도 컨테이너 이미지의 내용을 변경할 수 없다.&#x20;
* 도커 이미지는 \[저장소 이름]/\[이미지 이름]:\[태그] 의 형태로 구성된다.&#x20;

```sh
## Official 이미지는 [저장소 이름]을 붙여주지 않아도 됩니다.
$ docker pull ubuntu:latest
```

* 도커는 여러 레이어를 합쳐서 파일 시스템을 이룬다.&#x20;
  * 도커가 명령어로 commit, pull, push 등을 사용하는 이유를 다시 한 번 생각해본다.&#x20;

```sh
## ubuntu 컨테이너를 생성할 때, 어떤 순서로 진행했는지 확인해봅니다. 
$ docker history ubuntu
 
## ubuntu 이미지를 활용하여 commit_test라는 태그를 붙인 컨테이너를 생성합니다.
$ docker run -i -t --name commit_test ubuntu:latest

## 컨테이너 내부에 파일을 하나 생성합니다.
root@d7c8c6bf4aa4:/# echo test_first! >> first

## 컨테이너의 변경사항을 커밋합니다.
$ docker commit \
 -a "woowahancu" -m "my first commit " \
 commit_test \
 commit_test:first

## 커밋한 컨테이너 이미지가 추가되었음을 확인할 수 있습니다.
$ docker images
 
## 변경사항을 하나더 추가한 후 커밋합니다
$ docker run -i -t --name commit_test2 commit_test:first
 root@07640650696a:/# echo test_second! >> second
 
$ docker commit \
 -a "woowahancu" -m "my second commit " \
 commit_test2 \
 commit_test:second

## 각 이미지들사이에 추가된 Layer를 확인합니다.
$ docker inspect ubuntu:latest | jq '.[].RootFS.Layers'
$ docker inspect commit_test:first | jq '.[].RootFS.Layers'
$ docker inspect commit_test:second | jq '.[].RootFS.Layers'
 
$ docker inspect commit_test:first | jq '.[].GraphDriver'
$ docker inspect commit_test:second | jq '.[].GraphDriver'
 
## 커밋 변경사항을 확인해봅니다. 
$ cat /var/lib/docker/overlay2/[컨테이너ID]/diff/second

```

## 4. 도커 네트워크&#x20;

* veth interface&#x20;
  * 랜카드에 연결된 실제 네트워크 인터페이스가 아니라 가상으로 생성한 네트워크 인터페이스이다.
  * 일반적인 네트워크와는 다르게 패킷을 전달받으면, 자신에게 연결된 다른 네트워크 인터페이스로 패킷을 보내주기 때문에 항상 쌍으로 생성해야한다. &#x20;
* NET namespace&#x20;
  * 리눅스의 격리 기술인 namespace 중 네트워크와 관련된 부분을 말한다.&#x20;
  * 네트워크 인터페이스를 각각 다른 namespace 에 할당함으로써 서로가 서로를 모르게끔 설정할 수 있다.&#x20;
* 도커 네트워크 구조&#x20;
  * 위의 두 가지 개념을 이용하여 네트워크를 구성한다.&#x20;
  * mac 이나 window 는 veth interface 가 VM 안에 감춰져있어서 확인하기가 어렵다.&#x20;

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption><p><a href="https://joont92.github.io/temp/docker-network.png">https://joont92.github.io/temp/docker-network.png</a></p></figcaption></figure>

1. 컨테이너는 namespace 로 격리되고, 통신을 위한 네트워크 인터페이스 eth0을 할당받는다. (파란부분)
2. 호스트OS 의  veth interface가 생성되고, 컨테이너 내의 eth0 과 연결된다.&#x20;
3. host 의 veth interface 는 docker0라는 다른 veth interface 와 연결된다.&#x20;
   1. docker 0 는 도커 실행시에 자동으로 생성되는 가상의 브릿지이다.&#x20;
   2. 모든 컨테이너는 이 브릿지를 통해서 서로 통신이 가능하다.&#x20;
   3. [Switch 의 동작방식](https://brainbackdoor.tistory.com/115)을 참고한다.  &#x20;

```sh
## 도커를 생성하면 3가지 형태의 network가 생김을 확인할 수 있습니다.
$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
6b6ce553a425        bridge              bridge              local
81a18bc9cc40        host                host                local
576b0223f9cf        none                null                local

## bridge 네트워크를 확인해보면 172.17.0.0/16 대역을 할당했음을 확인할 수 있습니다.
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "6b6ce553a425c9392c5a65b8dcd2a57e1665289354b97f430758b745b1dc86a7",
        ...
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"

## 그리고 172.17.0.0/16 대역은 docker0로 매핑되어 있습니다.
$ ip route
default via 192.168.0.193 dev eth0 proto dhcp src 192.168.0.207 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1

## docker0는 veth interface와 매핑된 브릿지임을 확인할 수 있습니다.
$ brctl show docker0
bridge name	bridge id		STP enabled	interfaces
docker0		8000.024238d4b0f5	no		vethc8e309f

$ ip link ls
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
66: vethc8e309f@if65: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAULT group default

```

* docker-compose 로 띄우면, 다른 네트워크 대역을 가진다. compose 로 묶은 범위에 맞춰서 브릿지를 하나 더 생성하기 때문!&#x20;
  * 서로 공유하는 브릿지가 다르므로, docker-compose 로 띄운 컨테이너와 일반 컨테이너는 서로 통신할 수 없다.&#x20;

```sh
$ git clone https://github.com/woowacourse/service-practice.git
$ cd lb 
$ docker build -t node-server .
$ docker-compose -p practice up -d

$ sudo docker container inspect practice_lb_1 | jq '.[].NetworkSettings.Networks.practice_default.Gateway'
"172.18.0.1"

$ ip route
172.18.0.0/16 dev br-754310d33f5c proto kernel scope link src 172.18.0.1

$ ip link ls
4: br-754310d33f5c: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
   link/ether 02:42:cd:76:d1:f1 brd ff:ff:ff:ff:ff:ff

```

* 기본적으로 컨테이너는 외부와 통신이 불가하다. 포트포워딩을 설정하여 외부에 컨테이너를 공개할 수 있다.&#x20;

```sh
# 포트포워딩 설정과 함께 컨테이너를 생성합니다.
$ docker container run -d -p 8081:80 nginx
16cd67c48e5721a6b666192b8960875c720168bf6c5e3ed2138fb04c492447c6

$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
16cd67c48e57        nginx               "/docker-entrypoint.…"   4 seconds ago       Up 3 seconds        0.0.0.0:8081->80/tcp   trusting_bhabha

# Host의 8081포트가 listen 임을 확인합니다.
$ sudo netstat -nlp | grep 8081
tcp6       0      0 :::8081                 :::*                    LISTEN      10009/docker-proxy

# docker-proxy 라는 프로세스가 해당 포트를 listen 하고 있음을 볼 수 있습니다. 
# docker-proxy는 들어온 요청을 해당하는 컨테이너로 넘기는 역할만을 수행하는 프로세스입니다. 
# 컨테이너에 포트포워딩이나 expose를 설정했을 경우 같이 생성됩니다.

$ iptables -t nat -L -n
Chain DOCKER (2 references)
target     prot opt source               destination
RETURN     all  --  0.0.0.0/0            0.0.0.0/0
RETURN     all  --  0.0.0.0/0            0.0.0.0/0
RETURN     all  --  0.0.0.0/0            0.0.0.0/0
RETURN     all  --  0.0.0.0/0            0.0.0.0/0
DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8081 to:172.17.0.2:80

# 보시다시피 모든 요청을 DOCKER Chain 으로 넘기고, DOCKER Chain 에서는 DNAT를 통해 포트포워딩을 해주고 있음을 볼 수 있습니다.
# 이 iptables 룰은 docker daemon이 자동으로 설정합니다.

```

* docker container 의 네트워크 모드는 bridge, host, container, none 등 총 4개가 존재한다.&#x20;
  * [참고](https://bluese05.tistory.com/38) &#x20;

## 5. 도커 볼륨&#x20;

* 도커 이미지로 컨테이너를 생성하면, 이미지는 읽기 전용이 된다.&#x20;
* 컨테이너의 변경사항만 별도 저장해서 각 컨테이너의 정보를 보존한다.&#x20;
* 하지만 mysql 과 같이 컨테이너 계층에 저장되어있던 데이터베이스의 정보를 삭제해서는 안되는 경우도 있을 것이다. 컨테이너 데이터를 영속적인 데이터로 활용하기 위한 방법 중, 볼륨을 활용하는 방안이 있다.&#x20;
* \-v 옵션 : 호스트의 디렉터리를 컨테이너의 디렉터리에 마운트하나. 따라서 컨테이너의 해당 경로에 파일이 있었다면, 호스트의 볼륨으로 덮어 씌워진다.&#x20;

```sh
$ docker run -d \
--name wordpressdb_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABAS=wordpress \
-v /home/wordpress_db:/var/lib/mysql \
mysql:5.7

$ docker run -d \
--name wordpress_hostvolume \
-e MYSQL_ROOT_PASSWORD=password \
--link wordpressdb_hostvolume:mysql \
-p 80 \
wordpress

$ ls /home/wordpress_db
$ docker stop wordpressdb_hostvolume wordpress_hostvolume
$ docker rm wordpressdb_hostvolume wordpress_hostvolume
```

**이렇게 컨테이너가 아닌 외부에 데이터를 저장하고, 컨테이너는 그 데이터로 동작하도록 stateless 하게 설계하여야 한다. 컨테이너 자체는 상태가 없고, 상태를 결정하는 데이터는 외부로부터 제공받도록 한다.**&#x20;
