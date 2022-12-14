# 프로세스의 생성과 종료

### 프로세스의 생성

* 부모 프로세스가 자식 프로세스를 `복제` 생성한다.
* 프로세스의 트리(계층구조)를 형성한다.
* 자원을 필요로 한다.
  * 이 자원은 운영체제로부터 받는다.
  * 부모와 공유하는 경우도 있고, 아닌 경우도 있다.
* 자원의 공유
  * 부모와 자식이 모든 자원을 공유하는 모델
  * 일부를 공유하는 모델
  * 전혀 공유하지 않는 모델 : 이것이 일반적임. 일단 부모로부터 복제 생성된 자식은 하나의 별도 프로세스가 되기 때문에 그때부터는 자원을 얻기 위해서 부모와 자식도 경쟁을 한다.
* 수행
  * 부모와 자식은 공존하며 수행되거나
  * 자식이 종료(terminated)될때까지 부모가 기다리는(wait) 모델
* 주소공산
  * 자식은 부모의 공간을 복사한다. - binary and OS data
  * 그 공간에 새로운 프로그램을 올린다.
* 유닉스의 예
  1. `fork()` 시스템 콜이 새로운 프로세스를 생성한다.
     * 부모를 그래도 복사(os data except PID + binary)
     * 주소 공간을 할당
  2. fork 다음에 이어지는 `exec()` 시스템콜을 통해서 새로운 프로그램을 메모리에 올린다.

###

### 프로세스의 종료

* 자발적 종료 : 프로세스가 마지막 명령을 수행한 후, 운영체제에게 이를 알려준다. → `exit`
  * 자식이 부모에게 output data 를 보낸다. - via `wait`
  * 프로세스의 각종 자원들이 운영체제에게 반납됨
* 비자발적 종료 : 부모 프로세스가 자식의 수행을 종료시킴 → `abort`
  * 자식이 할당 자원의 한계치를 넘어섬
  * 자식이 할당된 태스크가 더 이상 필요하지 않음
  * 부모가 종료exit되는 경우
    * 운영체제는 부모 프로세스가 종료하는 경우, 자식이 더 이상 수행되지 않도록 두지 않는다.
    * 단계적인 종료 : 자식의 자식 → 자식 → 부모 순서대로 종료
