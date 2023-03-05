# aws로 그럴듯한 인프라 만들기



```sh
// Some code
ubuntu@ip-192-168-11-182:~/.ssh$ ssh -i key-mhson.pem ubuntu@192.168.11.32
The authenticity of host '192.168.11.32 (192.168.11.32)' can't be established.
ED25519 key fingerprint is SHA256:bf8Up22yCj3R3S7TRbgmG386jaujCC5gTOdAhcAz04w.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.11.32' (ED25519) to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0664 for 'key-mhson.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "key-mhson.pem": bad permissions
ubuntu@192.168.11.32: Permission denied (publickey).
```

```sh
// Some code
ubuntu@ip-192-168-11-182:~/.ssh$ chmod 400 key-mhson.pem
ubuntu@ip-192-168-11-182:~/.ssh$ ls -al
total 20
drwx------ 2 ubuntu ubuntu 4096 Mar  3 03:34 .
drwxr-x--- 4 ubuntu ubuntu 4096 Mar  3 03:34 ..
-rw------- 1 ubuntu ubuntu  391 Mar  3 03:11 authorized_keys
-r-------- 1 ubuntu ubuntu 1676 Mar  3 03:26 key-mhson.pem    # user 에게만 reading 권한 추가 
-rw-r--r-- 1 ubuntu ubuntu  142 Mar  3 03:34 known_hosts

```









## Trouble Shooting&#x20;

### 1. security group 설정 시, bastion 과 연결된 보안그룹으로 설정했는데 접속 오류!&#x20;

* 보안그룹으로 설정할 때에는 반드시 private ip 를 기준으로 연결설정을 해주어야한다.&#x20;
* 계속 public ip 로 연결해서 접속이 안되는 현상이 있었음.&#x20;
* 참고&#x20;
  * [https://rainbound.tistory.com/entry/AWS-보안그룹-규칙-소스에-보안그룹-넣기](https://rainbound.tistory.com/entry/AWS-%EB%B3%B4%EC%95%88%EA%B7%B8%EB%A3%B9-%EA%B7%9C%EC%B9%99-%EC%86%8C%EC%8A%A4%EC%97%90-%EB%B3%B4%EC%95%88%EA%B7%B8%EB%A3%B9-%EB%84%A3%EA%B8%B0)
  * [https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/security-group-rules.html](https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/security-group-rules.html)

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

ip 로 보안그룹 설정할 경우, 계속 ip 가 추가 되어야 하는 경우, 추가해주어야 함.&#x20;

이렇게 되면 bastion 보안그룹과 연결된 ec2 서버라면 모두 리소스에 접속이 가능함&#x20;

주의할 점&#x20;

* private ip 를 기반으로 처리되기 때문에 AWS 리소스만 추가할 수 있고, 외부 리소스, public ip 는 사용 불가함&#x20;
  * 주의: aws 서비스만 소스에 그룹을 넣어 반영이 가능합니다. 외부 ip의 경우는 적용이 되지않습니다.



## 기타 하면 좋은 설정들&#x20;

### 1. 세션 타임아웃 설정&#x20;

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### 2. 쉘 프롬프트 변경&#x20;

* [https://webdir.tistory.com/105](https://webdir.tistory.com/105)
* [https://ezprompt.net](https://ezprompt.net)

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## 빌드&#x20;

* 빌드할 때 테스트 없이 진행하기&#x20;
  * ./gradlew clean build -x test
*
