---
description: 3. 배포 스크립트 만들기
---

# aws로 그럴듯한 인프라 만들기 3 - 배포스크립트

> 이번 시리즈는 아래와 같이 진행된다. 이 글에서는 4번에 대해서 다룰 것이다. &#x20;
>
> 1. [PEM 키 생성하기 ](aws-3-1.md#1.-pem)
> 2. [망 구성하기 ](aws-1-1.md)
> 3. [배포하기](aws-2-1.md)&#x20;
> 4. [배포 스크립트 작성하기](aws-3-1.md)&#x20;



## 1. 배포 스크립트 작성&#x20;

우선 두괄식으로다가 내가 만든 배포 스크립트를 공개해보겠다.&#x20;

```sh
#!/bin/bash

## 변수 설정

txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray

BRANCH='miniminis'

## 함수 설정
### 저장소 변경사항 체크
function check_df() {
  git fetch

  master=$(git rev-parse $BRANCH)
  remote=$(git rev-parse origin/$BRANCH)
  echo -e ">>>>> master : $master"
  echo -e ">>>>> remote : $remote"

  if [[ $master == $remote ]]; then
    return 1
  fi

  return 0
}


### 저장소 pull
function pull() {
  echo -e "${txtgrn}>>>>> Pull Request 🏃♂️ 통해 코드를 업데이트 합니다. (from origin $BRANCH) ${txtrst}"
  git pull origin $BRANCH

  echo -e "${txtgrn}>>>>> origin $BRANCH 으로부터 성공적으로 Pull 완료되었습니다. ${txtrst}"
}

### 현재 process 조회 후 종료
function killProcess() {
  pid=$(pgrep -f subway-0.0.1-SNAPSHOT.jar)

  if [[ -n "$pid" ]]; then
    echo -e "${txtgrn}>>>>> 진행 중인 process($pid)가 존재하므로 종료합니다. ${txtrst}"
    kill -15 $pid
    echo -e "${txtgrn}>>>>> 프로세스가 정상적으로 종료되었습니다. ${txtrst}"
    return 0
  fi

  echo -e "${txtgrn}>>>>> 진행 중인 process가 없습니다. ${txtrst}"
}


## 배포 스크립트
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}   << subway의 배포를 시작합니다 🧐 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

echo -e "${txtgrn}>>>>> 원격 저장소의 변경사항을 체크합니다.${txtrst}"

check_df

if [[ $? -eq 1 ]];
then
  echo -e ">>>>> [$(date)] 업데이트 내용이 없으므로 프로그램을 종료합니다. 😫"
  exit 0
fi

echo -e ">>>>> [$(date)] 업데이트 내용이 있습니다. "
pull

echo -e "${txtgrn}>>>>> 현재 진행 중인 process가 있는지 조회하고 있다면, 종료합니다. ${txtrst}"
killProcess

echo -e "${txtgrn}>>>>> 새로운 앱 빌드를 실행합니다.${txtrst}"
./gradlew clean build -x test

echo -e "${txtgrn}>>>>> 빌드한 앱을 시작합니다. 실행 환경을 선택해주세요(local/prod) ${txtrst}"
read env

if [[ "$env" = "prod" ]]; then
  echo -e "${txtgrn}>>>>> 운영 환경에서 앱 배포를 시작합니다.${txtrst}"
  java -jar -Dspring.profiles.active=prod ./build/libs/subway-0.0.1-SNAPSHOT.jar &
elif [[ "$env" = "local" ]]; then
  echo -e "${txtgrn}>>>>> 로컬 환경에서 앱 배포를 시작합니다.${txtrst}"
  java -jar -Dspring.profiles.active=local ./build/libs/subway-0.0.1-SNAPSHOT.jar &
else
  echo -e "${txtgrn}>>>>> 존재하지 않는 환경입니다. 프로세스를 종료합니다. ${txtrst}"
  exit 0
fi

echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}<< subway의 배포가 성공적으로 완료되었습니다 🎉>>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"
```

사실 위의 스크립트는 코드를 읽을 줄 안다면, 어느정도는 이해하는데 무리가 없을 것이라고 생각한다. 그래서 아래에는 위의 스크립트를 만드는데 유용하게 사용했던 내용들을 몇 가지 소개할까한다.&#x20;



## 2. 뼈대 - 반복적으로 작성하는 명령어를 script 로 작성해보기&#x20;

```sh
#!/bin/bash

## 변수 설정

txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray


echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

## 저장소 pull
## gradle build
## 프로세스 pid를 찾는 명령어
## 프로세스를 종료하는 명령어
## ...


```



## 3. 기능 단위로 함수로 만들기&#x20;

* 기능 단위로 함수를 만드니까 나중에 오류가 발생했을 때나, 수정해야할 때, 알아보기가 쉬워서 좋았다.&#x20;

```sh
function pull() {
  echo -e ""
  echo -e ">> Pull Request 🏃♂️ "
  git pull origin master
}

pull;
```



## 4. 스크립트 실행시 파라미터를 전달해볼수도 있다

```sh
#!/bin/bash

## ...

EXECUTION_PATH=$(pwd)
SHELL_SCRIPT_PATH=$(dirname $0)
BRANCH=$1
PROFILE=$2

## 조건 설정
if [[ $# -ne 2 ]]
then
    echo -e "${txtylw}=======================================${txtrst}"
    echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
    echo -e ""
    echo -e "${txtgrn} $0 브랜치이름 ${txtred}{ prod | dev }"
    echo -e "${txtylw}=======================================${txtrst}"
    exit
fi

## ...

```

* 실행시 파라미터를 전달하도록 하여 범용성 있는 스크립트를 작성해본다.&#x20;
* read 명령어를 활용하여 사용자의 Y/N 답변을 받도록 할 수도 있다.&#x20;

##

## 5. github branch 변경이 있는 경우 감지하기&#x20;

* git branch 에 변경이 있는 경우, 이를 감지하여 스크립트가 동작되도록 작성해본다.&#x20;

```sh
function check_df() {
  git fetch
  master=$(git rev-parse $BRANCH)
  remote=$(git rev-parse origin/$BRANCH)

  if [[ $master == $remote ]]; then
    echo -e "[$(date)] Nothing to do!!! 😫"
    exit 1
  fi
}
```



## 6. crontab을 활용하기&#x20;

* 매 분마다 동작하도록한 후 log를 확인해보자.&#x20;
* crontab과 /etc/crontab의 차이에 대해 학습해보자.&#x20;

