# 그 외 개념 정리

## 기약분수&#x20;

* 분자와 분모의 공약수가 1뿐이어서 더이상 약분되지 않는 분수이다.&#x20;
* 분자와 분모가 서로소이며 최대 공약수가 1이라는 것을 의미한다.&#x20;
* 약분을 위한 공약수는 유클리드 호제법으로 찾아낼 수 있다.&#x20;
* ```java
  //전제 : p > q
  public static int gcd(int p, int q)
   {
  	if (q == 0) {
  		return p;
  	}
  	return gcd(q, p%q);
   }
  ```
* 참고
  * [위키백과](https://ko.wikipedia.org/wiki/%EA%B8%B0%EC%95%BD%EB%B6%84%EC%88%98) &#x20;
