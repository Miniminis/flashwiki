# 어노테이션 프로세서 정리하기

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}



## 1. 개념 &#x20;

* 애노테이션 프로세서는 소스코드 레벨에서 소스코드에 붙은 에노테이션을 이용하여 컴파일러가 컴파일 하는 중에 새로운 소스코드를 생성하거나 기존의 소스코드를 바꿀 수 있는 기능이다.&#x20;

## 2. 사용 예시&#x20;

* Lombok &#x20;
* [AutoService](https://github.com/google/auto/tree/main/service) : java.util.ServiceLoader 용 파일 생성 유틸리티&#x20;
* [@Override](https://stackoverflow.com/questions/18189980/how-do-annotations-like-override-work-internally-in-java/18202623)&#x20;
  * 오버라이드 대상이 아닌 매소드에 붙였을 때에는 컴파일 에러가 난다.&#x20;
* [Dagger 2](https://github.com/google/dagger)&#x20;
  * 컴파일 타임에 dependency injection 을 제공해주는 라이브러리&#x20;
* Android Library&#x20;
  * [ButterKnife](http://jakewharton.github.io/butterknife/) : @BindView - 뷰 아이디와 에노테이션 붙인 필드를 바인딩해준다.&#x20;
  * [DeepLinkDispatch](https://github.com/airbnb/DeepLinkDispatch) : 특정 URI 링크를 Activity 로 연결할 때 사용된다.&#x20;

## 3. 어노테이션 프로세서 특징 &#x20;

* 장점&#x20;
  * 런타임 비용이 제로이다.&#x20;
    * 컴파일 할 때, 이미 뭔가 조작된 상태이다. 따라서 런타임 시에는 추가되는 비용이 없다.&#x20;
* 단점&#x20;
  * 합법적(?)인 public api 가 없다.&#x20;
  * lombok 처럼 해킹을 통해 소스코드를 변경하는 방법밖에는 없다. (논란)&#x20;

