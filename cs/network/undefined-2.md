# 웹 브라우저의 캐시 - 세션 스토리지

## 1. 개념&#x20;

* 세션 스토리지는 로컬 스토리지와 매우 유사하다.&#x20;
* key와 value 형태로 저장되고, 오리진에 종속적이다.&#x20;
* 하나의 키에는 오직 하나의 값만 저장되고, 최대 크기는 5mb 이다 .
* 차이점&#x20;
  * 탭이나 창을 닫는 순간 바로 데이터가 만료된다.&#x20;

## 2. 사용법&#x20;

* 설정&#x20;
  * sessionStorage.setItem(key, value);&#x20;
* key 에 해당하는 value 가져오는 법&#x20;
  * sessionStorage.getItem(key);
* 제거&#x20;
  * sessionStorage.removeItem(key);
* 전체 제거&#x20;
  * sessionStorage.clear();



일반적으로는 세션스토리지보다는 로컬스토리지를 많이 사용한다.&#x20;

