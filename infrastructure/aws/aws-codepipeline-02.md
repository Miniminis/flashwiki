---
title: AWS CodePipeline 으로 Spring Boot 프로젝트 배포 자동화 구축하기(2) - CodeDeploy
category: aws
date: 2021-12-19 09:34:52
---

> [동욱님의 AWS로 배포하기 시리즈 포스팅](https://jojoldu.tistory.com/281?category=777282)을 하나씩 따라하며 마주했던 이슈 및 해결방법들을 덧붙여서 AWS CodePipeline 으로 Spring Boot 프로젝트의 배포를 자동화하는 과정을 진행중이다.

지난번 포스팅을 통해서 1) 전체 AWS Pipeline 의 큰 프로세스를 훑어보고 2) 소스, 빌드, 배포의 파이프라인을 설정하는 부분까지 살펴보았다.  

이번 포스팅을 통해서는 추가적으로 필요한 설정들을 살펴보고 각 단계별로 진행이 잘 되는지 직접 파이프라인을 실행하면서 확인해보도록 하자. 

그럼, 아래의 그림을 기억하며 배포 스테이지를 추가하여 완성된 파이프라인을 실행해본다.  

![Untitled](/.gitbook/assets/aws-pipeline-02-0.png)

## 소스 스테이지 실행하기

1) 테스트를 위해 파이프라인과 연결된 브랜치에 커밋을 푸시하게 되면, 2) 파이프라인은 변경사항을 감지하고 최신소스로 업데이트를 할 것이다. 3) 소스가 제대로 가져와 졌다면 S3 에 아티팩트로 저장된다. S3 로 이동하여 제대로 아티팩트가 만들어졌는지 확인한다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-1.png)

- 파이프라인에 의해서 자동으로 S3 버킷이 생성되었고,

![Untitled](/.gitbook/assets/aws-pipeline-02-2.png)

![Untitled](/.gitbook/assets/aws-pipeline-02-3.png)

![Untitled](/.gitbook/assets/aws-pipeline-02-4.png)

- 파이프라인 이름으로 디렉토리가 생성되었다. SourceArti 디렉토리 내부를 들어가보면
- SourceArtifact 가 정상적으로 생성되었음을 알 수 있다.

### 이슈) 403 권한오류가 발생할 때

![Untitled](/.gitbook/assets/aws-pipeline-02-5.png)

- 파이프라인 생성 당시에 직접 만든 S3 에 소스 아티팩트를 보관하도록 처리하는 경우, S3 리소스에서 정책과 접근권한이 제대로 설정되어있지 않다면 다음과 같이 소스 다운로드 과정에서 권한오류가 발생할 수 있다.
- 하지만 파이프라인 쪽에서 S3 아티팩트 보관용 버킷을 자동 생성하도록 두어져있다면, 해당 이슈는 우려하지 않아도 된다.

## 빌드 스테이지 실행하기

소스 스테이지에서 정상적으로 통과가 되면, 자동으로 빌드 스테이지로 넘어오게 된다. 현재 상태로는 빌드에 필요한 추가 설정들을 하지 않았기에, 아마 실패할 것이다. 아래를 따라서 빌드에 필요한 추가 설정들을 해보자. 

### 프로젝트 내부 > buildspec.yml 생성하기

> [AWS CodeBuild 공식문서를](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html) 통해 기타 다른 설정들을 참고할 수 있다.
> 

소스의 출처가 되는 원격 레포지토리의 구조에 따라 세부 경로가 달라지게 되는데,  `gradlew` 가 있는 곳이 루트디렉토리가 되게끔 설정을 해주면 된다. 나 같은 경우는 아래와 같이 되어있어서 한 단계 더 들어가서 설정해주도록 하였다. 

```
//git 원격 레포지토리 내 디렉토리 구조

repository root 
L test-project 
	L gradle/wrapper
	L src
	L buildspec.yml
	L build.gradle
	L gradlew
	L gradlew.bat
	L setting.gradle
L .gitignore
L README.md
```

```yaml
# buildspec.yml 

version: 0.2

phases:
  build:
    commands:
      - echo Entered the build phase...
      - echo Build Starting on `date`
      - cd test-project   # 루트에서 프로젝트로 한 depth 더 이동하기
      - chmod +x ./gradlew  # gradlew 가 있는 위치에서 빌드 명령어 시작
      - ./gradlew clean build
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`
      - echo $(basename ./build/libs/*.jar)
      - pwd

cache:
  paths:
    - '/root/.gradle/caches/**/*'
```

- build : 프로젝트의 빌드가 시작되는 시점이다. gradlew 실행 권한을 부여하고, 클린 후 빌드 할 수 있도록 명령어를 추가한다.
- post_build : 빌드가 끝난 시점. 빌드 결과물을 출력해서 빌드가 성공적으로 되었는지 확인한다.
- cache : 해당 위치에 있는 파일들을 S3 캐시 파일로 등록한다.

### 빌드 성공

위의 설정을 마치면 다시 소스코드를 커밋&푸시하여 파이프라인 프로세스를 시작한다. 소스 단계가 통과되고 빌드가 성공적으로 마무리 되면, 아래와 같이 콘솔 창에서 SUCCEEDED 를 확인 가능하다. 콘솔창을 통해서 우리가 buildspec.yml 에 설정한 각 빌드 단계별 명령어가 성공적으로 수행되었음을 알 수 있다.  

![Untitled](/.gitbook/assets/aws-pipeline-02-6.png)

![Untitled](/.gitbook/assets/aws-pipeline-02-7.png)

빌드된 결과물은 역시 S3에 아티팩트로 저장되는데 빌드가 성공적으로 진행된 이후 S3 내에서 확인 가능하다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-8.png)

디렉토리 내부에 빌드된 파일이 제대로 생성되었다면 성공적으로 마무리 된 것이다. 

### 이슈) buildspec.yml 파일을 찾을 수 없을 때

![Untitled](/.gitbook/assets/aws-pipeline-02-9.png)

빌드 프로세스가 일단 시작되면, 파이프라인은 buildspec.yml 파일을 찾는다. 1편에서 다뤘던 빌드 프로세스 설정시 buildspec.yml 경로를 잘못 입력했을 경우,  buildspec.yml 파일을 찾지 못하여 빌드에 실패하게 된다. 따라서 경로 설정이 정말 중요하다. 빌드 시 콘솔 창에서 그 과정들이 메시지로 출력되므로 오류시에는 오류 메시지를  천천히 읽고 그에 맞게 대응하는 것이 중요하다. 

### 이슈) buildspec.yml 내부의 경로 설정이 잘못되었을 때

![Untitled](/.gitbook/assets/aws-pipeline-02-10.png)

빌드 프로세스를 생성할때 경로설정을 잘 했다고 하더라도, buildspec.yml 파일 내부에서 명령어를 잘 못 입력할 경우, `gradlew` 파일을 찾을 수 없다는 오류가 발생해서 빌드에 실패하게 된다. 앞서 말한대로 build 단계에서 본인의 `gradlew` 파일이 있는 곳까지 가서 빌드 명령어를 잘 수행할 수 있도록 디렉토리 위치를 이동하는 명령어를잘 설정해주어야 한다. 위에서는  `cd test-project` 명령어를 추가하여 `gradlew` 가 있는 위치까지 디렉토리를 이동해주었다. 

## 배포 스테이지 실행하기

빌드가 성공적이라면 역시 자동으로 배포 스테이지로 이동하게 된다. 배포 스테이지 역시 추가적인 설정들이 더 필요하기에 배포에 실패할 것이다. 아래를 통해 추가 설정 후 다시 배포 스테이지를 진행해보자. 

### IAM > EC2 서버를 위한 역할 생성하기

우선 운영중인 EC2 서버가 있다고 가정하고 (없다면 하나 만들고) EC2 서버의 역할을 수정해주어야 한다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-11.png)

- IAM > 역할 > EC2 를 선택한 후 다음을 누른다.

아래의 4가지 정책을 추가해준다. 

- AmazonS3FullAccess
- AWSCodeDeployFullAccess
- AWSCodeDeployRole
- CloudWatchLogsFullAccess

태그도 필요할 경우 추가해주고, 아래와 같이 역할 이름을 추가 후 정책들을 검토한다. 문제 없을 경우 역할 만들기를 클릭하여 완료한다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-12.png)

만들어진 역할을 운영중인 EC2 서버에 추가한다. 

- 연결된 IAM 역할 없는지 확인 : EC2 > 인스턴스 클릭 > 작업 > 세부정보보기
- IAM 역할 연결하기 : 작업 > 보안 > 위에서 만들어준 IAM 역할로 수정

### IAM > EC2 에서 CodeDeploy Agent 용 사용자 추가하기

CodeDeploy 에서 배포가 시작되면 EC2 서버에서는 그 이벤트를 수신할 수 있어야 한다. 그를 위해서 이 이벤트를 수신하기 위한 CodeDeploy Agent 용 사용자를 추가해주어야 한다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-13.png)

IAM 에서는 1) 그룹을 만들고 2) 그 그룹에 정책을 연결한 뒤 3) 해당 그룹에 들어갈 사용자를 추가해주는 방식으로 관리가 되므로 먼저 그룹을 만들어준다. (이 부분이 이해가 안간다면 [이 포스팅](https://blog.mhson.world/2021/12/20/aws/aws-iam/)을 참고할 것)

그룹 생성을 완료하면 인라인 정책으로 아래와 같은 정책을 추가해준다. 

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "autoscaling:*",
                "codedeploy:*",
                "ec2:*",
                "lambda:*",
                "elasticloadbalancing:*",
                "s3:*",
                "cloudwatch:*",
                "logs:*",
                "sns:*"
            ],
            "Resource": "*"
        }
    ]
}
```

![Untitled](/.gitbook/assets/aws-pipeline-02-14.png)

그 후, 사용자를 추가해준다. aws-cli 를 이용해서만 엑세스할 것이기 때문에 프로그래밍 방식을 선택해준다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-15.png)

- 아까 만든 그룹을 선택한다.

필요할 경우 태그를 추가한 뒤, 내용을 검토 후 사용자 생성을 마무리한다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-16.png)

- csv 파일은 이 페이지에서만 다운로드 가능하니, 다운로드후 잘 관리하도록 한다. 파일 내부의 엑세스키와 시크릿키를 이용하여 aws-cli 에 접속할 것이다.

### EC2 > AWS Cli 설치하기

AWS CodeDeploy 로부터 이벤트를 수신받기 위해서 EC2 서버 내에 CodeDeploy Agent 를 설치해야합니다. 먼저 AWS Cli 를 설치해보죠. 

1. 사용자 홈으로 이동 : `cd ~`
2. aws 버전 확인 : `aws --version` 
        ```bash
        Command 'aws' not found, but can be installed with:
        sudo apt install awscli
        ```
3. awscli 설치 : `sudo apt install awscli`
    ```bash
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    ```
4. 설치확인 : `aws --version`
    ```bash 
   aws-cli/1.18.69 Python/3.8.10 Linux/5.11.0-1022-aws botocore/1.16.19
   ```

### EC2 > AWS CodeDeploy Agent 설치하기

aws cli 를 설치한 뒤, 이어서 CodeDeploy agent 를 설치합니다. 

1. 우선 사용자 홈으로 이동 : `cd ~`
2. aws configure 시작 : `sudo aws configure`
3. 정보입력
    
    ```bash
    AWS Access Key ID [None]: # 아까 생성한 CodeDeploy Agent 사용자의 .csv 파일 참고
    AWS Secret Access Key [None]: # 아까 생성한 CodeDeploy Agent 사용자의 .csv 파일 참고
    Default region name [None]: ap-northeast-2
    Default output format [None]: json
    ```
    
4. agent 설치파일 다운받기 : `wget https://aws-codedeploy-ap-northeast-2.s3.amazonaws.com/latest/install`
5. 실행파일에 권한 추가하기 : `chmod +x ./install`
6. 권한 추가되었는지 확인하기 : `ls -al`
7. 설치 시작 : `sudo ./install auto`
    1. 이슈 발생시 하단 참고!
8. 설치 완료되면, agent가 실행중인지 확인 : `sudo service codedeploy-agent status` 
        ```bash
        ● codedeploy-agent.service - LSB: AWS CodeDeploy Host Agent
        Loaded: loaded (/etc/init.d/codedeploy-agent; generated)
        Active: active (running) since Fri 2021-12-17 09:02:49 UTC; 15s ago
        Docs: man:systemd-sysv-generator(8)
        Tasks: 2 (limit: 1147)
        Memory: 60.4M
        CGroup: /system.slice/codedeploy-agent.service
        ├─25961 codedeploy-agent: master 25961
        └─25963 codedeploy-agent: InstanceAgent::Plugins::CodeDeployPlugin::CommandPoller of master 25961
        ``` 
9. EC2 인스턴스가 부팅되면 자동으로 AWS CodeDeploy Agent 가 실행될 수 있도록 스크립트 파일 생성
    1. `sudo vim /etc/init.d/codedeploy-startup.sh`
    2. 스크립트 파일내용
        ```bash
        #!/bin/bash 
        echo 'Starting codedeploy-agent' 
        sudo service codedeploy-agent restart 
        ```
    3. 파일 내용 확인 : `cat /etc/init.d/codedeploy-startup.sh`
    4. 스트립트 파일 저장 후, 실행권한 추가 : `sudo chmod +x /etc/init.d/codedeploy-startup.sh`

### 이슈) `sudo ./install auto` 실행시 `/usr/bin/env: ‘ruby’: No such file or directory`오류 발생할때

- 동욱님 포스팅을 따라하던 중, 서버 환경이 달라서인지 나는 `sudo ./install auto` 이 명령어를 입력하니 루비가 없다는 오류메시지가 떴었다.
- 루비가 없다고 하니, 루비를 설치해주기로 했다.
    - 참고
        - [09 1. AWS CodeDeploy 활용](https://github.com/sftth/cicd/wiki/09-1.-AWS-CodeDeploy-%ED%99%9C%EC%9A%A9)
        - [(공식문서) 우분투에 루비 설치하기](https://linuxize.com/post/how-to-install-ruby-on-ubuntu-18-04/)
    - 실행 스크립트
        
        ```bash
        //우선 apt package update
        $ sudo apt update
        
        //ruby 설치
        $ sudo apt install ruby-full
        
        //설치 확인을 위한 ruby 버전 확인
        $ ruby --version
        ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x86_64-linux-gnu]
        ```
- 루비 설치 후 다시 명령어 실행하니까 정상적으로 설치 완료되었다. 
        

### 프로젝트 내부 > buildspec.yml 수정하기

> 추가적인 설정은 [AWS 공식문서](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-name-storage)를 참고하자.
> 

EC2 내부에 CodeDeploy 용 agent 까지 설치를 완료했다면, 프로젝트로 다시 이동하여 배포를 위해 buildspec.yml 파일을 수정해야한다. 

지난번에 설정한 내용은 코드 빌드를 위해서 각 빌드 단계별 (빌드시, 빌드 후 등) 명령어를 세팅해뒀고 캐싱을 위해 경로정도만 설정하였다. 여기에 더하여 빌드 이후 배포 단계로 넘어갈 때, 프로젝트에서 빌드 결과물인 아티팩트에 같이 포함할 파일들을 설정해줄 수 있는데, 우리는 다음과 같은 설정으로 파일들을 포함할 것이다.   

```yaml
version: 0.2

phases:
  build:
    commands:
      - echo Entered the build phase...
      - echo Build Starting on `date`
      - cd arirang-first
      - chmod +x ./gradlew
      - ./gradlew clean build -x test
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`
      - echo $(basename ./build/libs/*.jar)
      - pwd

###################################################
# 코드를 빌드한 이후 빌드 결과물인 아티팩트에 포함시키기 위하여 새롭게 추가된 내용 
artifacts:
  files:
    - appspec.yml
    - build/libs/*.jar
    - scripts/**
  discard-paths: yes
  base-directory: arirang-first
###################################################

cache:
  paths:
    - '/root/.gradle/caches/**/*'
```

- 다른 부분은 build 단계에서 최초로 생성할때와 다르지 않다.
- artifacts 부분은 빌드 결과물에 포함할 파일들을 지정하는 것이다.
    - `appspec.yml`: AWS CodeDeploy 가 어떤 파일들을 어느 위치에 배포할지, 그리고 그 후에는 어떤 스크립트를 실행시킬건지 모두 관리하는 역할을 한다.  아래에서 추가해줄 예정이다.
    - `buid/libs/*.jar` : 빌드 결과로 생성된 jar 파일
    - `scripts/**`: 배포 이후 실행시킬 스크립트를 저장할 디렉토리이다. 없다면 루트 위치에 하나 만들어주자.
    - `discard-paths` : 절대경로를 버리고 루트/파일명으로 설정한다.
    - `base-directory` : 기본 루트 디렉토리를 설정해준다. 없으면 그냥 해당 설정 자체를 없엔다.

### 프로젝트 내부 > appspec.yml 생성하기

앞서 말한대로 AWS CodeDeploy 가 어떤 파일들을 어느 위치에 배포하는지, 배포 후에는 어떤 스크립트를 실행하는지 관리하는 곳이 바로 이 `appspec.yml` 파일이다. buildspec.yml 이 있는 같은 디렉토리 상에서 파일을 생성해준다. 

```
//디렉토리 구조

repository root 
L test-project 
	L gradle/wrapper
	L src
	L appspec.yml
	L buildspec.yml
	L build.gradle
	L gradlew
	L gradlew.bat
	L setting.gradle
L .gitignore
L README.md
```

```yaml
# appspec.yml 

version: 0.0
os: linux
files:
  - source: /
    destination: /home/ubuntu/arirang-first/build/

permissions:
  - object: /
    pattern : "**"
    mode : 755
    owner : ubuntu
    group : ubuntu

hooks:
  ApplicationStart:
    - location: deploy.sh
      timeout: 60
      runas: ubuntu
  ValidateService:
    - location: healthCheck.sh
      timeout: 60
      runas: ubuntu
```

- files
    - 소스 스테이지에서 받은 전체 파일들을 EC2 서버 내부의 `/home/ubuntu/arirang-first/build/`로 옮겨준다는 뜻 (EC2 내부에 해당 위치에 미리 `build` 디렉토리를 생성해두자! )
- permissions
    - files 섹션의 파일 및 디렉터리/폴더가 인스턴스에 복사된 후 특수 권한(있는 경우)이 어떻게 적용되어야 하는지를 지정하는 것이다.
    - 우리는 ubuntu 사용자 및 그룹으로 로그인하고 전체 파일에 대하여 읽고 실행할 수 있는 권한이 있어야하므로 755 라고 권한을 주었다. mode 부분이 이해가 안간다면 리눅스 권한부여 부분을 이해해야 한다. 아래의 공식문서에서 해당 내용을 설명해주니 참고해보자.
    - 자세한 내용은 [AWS 공식문서](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/reference-appspec-file-structure-permissions.html)를 참고해보자
- hooks
    - ApplicationStart : 배포된 앱이 시작될 때 발생하는 훅이벤트이다. 이 훅을 받았을 때, 미리 정해놓은 위치의 쉘스크립트를 실행하도록 설정했다.
    - ValidateService : 배포된 앱이 제대로 실행되었는지 확인하는 이벤트이다. 역시 이때 실행할 스크립트를 미리 지정해둔다.
    - [기타 다른 훅 및 상세한 설명은 AWS 공식문서 참고](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html#reference-appspec-file-structure-hooks-list)

### 프로젝트 내부 > 배포용 쉘 스크립트 생성하기

appspec.yml 을 통해서 배포 후 어떤 쉘 스크립트를 실행할지 설정을 해주었다. 그를 위해 루트 디렉토리에 `scripts` 폴더를 만들고 아래의 스크립트 2개를 추가해주자. 

```bash
# scripts > deploy.sh

#!/bin/bash
echo "> 아리랑 프로젝트 어플리케이션 배포 시작"

BUILD_PATH=/home/ubuntu/arirang-first/build/arirang-first-0.0.1.jar
JAR_NAME=$(basename $BUILD_PATH)
echo "> build 파일명: $JAR_NAME"

echo "> build 파일 복사"
DEPLOY_PATH=/home/ubuntu/arirang-first/
cp $BUILD_PATH $DEPLOY_PATH

echo "> arirang-first-0.0.1.jar 교체"
CP_JAR_PATH=$DEPLOY_PATH$JAR_NAME
APPLICATION_JAR_NAME=arirang-first-0.0.1.jar
APPLICATION_JAR=$DEPLOY_PATH$APPLICATION_JAR_NAME

ln -Tfs $CP_JAR_PATH $APPLICATION_JAR

echo "> 현재 실행중인 애플리케이션 pid 확인"
CURRENT_PID=$(pgrep -f $APPLICATION_JAR_NAME)

if [ -z $CURRENT_PID ]
then
  echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다."
else
  echo "> kill -15 $CURRENT_PID"
  kill -15 $CURRENT_PID
  sleep 15
fi

echo "> $APPLICATION_JAR 배포"

CURRENT_SERVER_ADDRESS=$(hostname -I)
echo "> CURRENT_SERVER_ADDRESS : $CURRENT_SERVER_ADDRESS"

if [ ${CURRENT_SERVER_ADDRESS} = "10.2.10.20" ]
then
  echo "> 운영서버이므로 운영 환경으로 배포합니다."
  java -jar -Dspring.profiles.active=prod $APPLICATION_JAR > /dev/null 2> /dev/null < /dev/null &
else
  echo "> 개발서버이므로 개발 환경으로 배포합니다."
  java -jar $APPLICATION_JAR > /dev/null 2> /dev/null < /dev/null &
fi

echo "> 서버가 성공적으로 배포되었습니다. "
```

- 기본적으로 동욱님이 제공해주신 스크립트에 실행환경부분을 구별하여 명령어를 분기할 수 있도록 커스텀하였다.
- BUILD_PATH 에서 jar 파일명만 추출하여 배포하고자 하는 곳에 해당 파일명으로 소프트 링크를 만든다.
- 현재 실행중인 프로세스 아이디 확인 후
    - 실행중인 프로세스가 있다면 중단시키고
    - 없다면 종료하지 않고 그대로 진행한다.
- Jar 파일을 배포하는데, 개발과 운영 환경에 따라서 다른 profile 을 적용하여 jar 파일을 실행해야하므로 현재 서버의 호스트 네임을 조회한 뒤, 해당 결과값에 따라서 파일 실행 명령어를 분기처리한다.
- 참고
    - [현재 서버 hostname 가져오는 쉘 명령어](https://kldp.org/node/130136) : hostname -I

```yaml
# scripts > healthCheck.sh

#!/bin/bash
echo "> Arirang Health check 시작"
echo "> ps -ef | grep java"

for RETRY_COUNT in {1..15}
do
  RESPONSE=$(ps -ef | grep java)
  UP_COUNT=$(echo $RESPONSE | grep 'arirang-first-0.0.1.jar' | wc -l)

  if [ $UP_COUNT -ge 1 ]
  then # $up_count >= 1 ("UP" 문자열이 있는지 검증)
      echo "> Health check 성공"
      break
  else
      echo "> Health check의 응답을 알 수 없거나 혹은 status가 UP이 아닙니다."
      echo "> Health check: ${RESPONSE}"
  fi

  if [ $RETRY_COUNT -eq 10 ]
  then
    echo "> Health check 실패. "
    exit 1
  fi

  echo "> Health check 연결 실패. 재시도..."
  sleep 10
done
exit 0
```

- 15번간 재시도를 하는데, 현재 앱 프로세스가 실행중이라면, UP_COUNT 가 1보다 커지게 되어 healthCheck 에 성공하게 된다.
- healthCheck 에 실패하게 되면 다시 시도하게 된다.
- 10번까지 재시도를 하게 되면 healthCheck 에 실패하여 스크립트가 비정상종료를 하도록 하였다.

### 배포 스테이지 실행하기

위의 설정들을 추가한 뒤, 다시 프로젝트 내 변경사항을 커밋 & 푸시하여 소스 → 빌드 → 배포 스테이지를 실행한다. 배포가 성공적으로 완료된다면, 아까 buildspec.yml 내에 설정했던 것처럼 정해둔 아티팩트들이 EC2 서버 내로 이동한 것을 확인 가능하다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-17.png)

서버 내에서 실행중인 프로세스도 확인 가능하다. 

![Untitled](/.gitbook/assets/aws-pipeline-02-18.png)

### 이슈) S3 접근 권한 오류 : The provided role does not have sufficient permissions.

```bash
Unable to access the artifact with Amazon S3 object key 'arirang-pipeline/BuildArtif/mDNbhDK' located in the Amazon S3 artifact bucket 'arirang-deploy'. The provided role does not have sufficient permissions.
```

- 간혹 배포 프로세스 실행 중에 위와 같은 오류메시지를 뱉으며 실패하는 경우가 있다.
- 나의 경우 원인은 다양했는데 대략 아래와 같은 경우들이었다.
    - buildspec.yml YAML 파일 내 문법 오류
        - 내부에서 artifacts 의 `base-directory` 경로를 설정해주지 않아서 BuildArtifact 에 포함할 파일들의 위치를 찾지 못하였을 때 appspec.yml 파일들 찾아서 포함시켜주지 못해서 오류가 발생한다.
        - 내부에서 artifacts 관련 YAML 파일 내 문법이 오류가 있었을 때
    - appspec.yml 파일 내에서 permission 세팅에 오류가 있었을 때

### 이슈) 배포 스테이지 설정시 로드밸런서 설정을 했을 경우

배포 스테이지를 생성할 때 실수로 로드밸런싱 활성화를 체크한 적이 있었다. 트래픽 관련 이벤트 때문에 일반적으로 구성한 배포 스테이지보다 실행 이벤트가 굉장히 많아진 것을 볼 수 있다. 실수로 체크하지 않도록 주의하자. 

![Untitled](/.gitbook/assets/aws-pipeline-02-19.png)

![Untitled](/.gitbook/assets/aws-pipeline-02-20.png)

![Untitled](/.gitbook/assets/aws-pipeline-02-21.png)

## (보너스) EC2 서버 내 CodeDeploy 로그 확인하기위한 소프트링크 연결하기

EC2 서버 내부에는 배포 이후 되도록 지정한 스크립트가 실행된 내용이 로그로 남겨지고 있다. 그 위치가 너무 멀리 있기 때문에 홈 디렉토리에서 소프트링크로 연결해보도록 하자. 

```yaml
# ln -s 파일또는디렉토리경로 바로가기명

ln -s /opt/codedeploy-agent/deployment-root/ deployment-root
ln -s /home/ubuntu/holostanding-admin/deployment-root/deployment-logs/codedeploy-agent-deployments.log codedeploy-agent-deployments.log
```