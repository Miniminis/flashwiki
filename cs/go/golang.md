# golang

### 역사

* 2009 년에 만들어짐
* 켄 톰슨, 롭파이크 두 개발자에 의해 만들어짐
  * 켄 톰슨
    * B언어를 만든 사람(C언어 이전에 A언어, B언어가 있었다…!)
  * 롭파이크
    * UTF-8을 만든 사람
      * UTF-16 : 모든 문자를 2byte 로 표현
      * 롭파이크는 대부분의 문자표현은 영문자와 숫자이고 한자와 아라비아어는 가끔 쓰일 뿐인데, 매번 낭비되는 이 공간이 아깝다고 생각함.
      * 그래서 영문자, 숫자는 1byte, 그 이외는 2-3byte로 표현할 수 있도록 UTF-8을 고안해냄

### golang

* C언어와 닯아있고 UTF-8을 지원함
* 굉장히 powerful 함
* 시스템 프로그래밍을 하기 위해 고안해낸 언어
* 현대적 언어 + 생산성 + 안정적