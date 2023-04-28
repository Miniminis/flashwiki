# github organization private repository push 안될 때 (not found issue)

그동안 두 곳의 회사를 거치면서 gitlab, bitbucket 등을 써왔고, 이번에는 사이드 프로젝트 팀원들과 함께 github 를 형상관리툴 및 코드저장소로 사용하게 되었다. github organization 은 처음이였던지라 첫 세팅부터 조금 삽질을 해버렸다.&#x20;



## 1. 문제&#x20;

* github organization 계정에서 clone 한 repository 에 push 가 안되는 현상&#x20;
  * 정확한 오류 메시지로는 Repository not found 로 존재하는 repository 가 존재하지 않는다고 메시지가 자꾸 뜨는 것...!&#x20;



## 2. 해결 방법&#x20;

방법을 찾던 중, 나와 정확하게 같은 상황을 겪고 있는 분의 [블로그](https://tape22.tistory.com/27)를 만났다...! 이분의 해결방법은 대략 아래와 같았다.&#x20;

1. 권한체크&#x20;
   1. Organization - Owner, Repository - Admin 권한이 있는지 확인&#x20;
   2. 권한이 없다면 추가&#x20;

<figure><img src="../../.gitbook/assets/Group 5 (1).png" alt=""><figcaption></figcaption></figure>

하지만 나의 경우, 두 개의 권한이 모두 있음에도 계속 같은 오류가 반복되는 현상을 겪었다.&#x20;

* org 차원, repo 차원 최고 권한임에도 계속 repo not found error 반복
* 사용 : command line + delete all github mac keychain + use token 사용&#x20;
* 참고&#x20;
  * [https://stackoverflow.com/questions/10116373/git-push-error-repository-not-found](https://stackoverflow.com/questions/10116373/git-push-error-repository-not-found)
