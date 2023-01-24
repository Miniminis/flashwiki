# Data Format - JSON

## 데이터

* 데이터는 이론을 세우는 데 기초가 되는 사실, 또는 바탕이 되는 자료나 관찰, 실험, 조사 등으로 얻은 사실이나 자료를 의미한다.&#x20;
* 데이터는 복잡하다. 때문에 데이터를 주고 받을 때에는 어떤 약속(포맷)을 정해서 주고 받아야 한다. 이러한 포멧에는 여러가지 종류가 있다.  &#x20;

## 개념

* JSON은 Javascript Object Notation 의 약자로, 자바스크립트 객체 문법으로 구조화된 데이터를 표현하기 위한 표준 포맷이다.&#x20;
* key-value 형태로 나타내고, 중간에 콜론을 이용하여 구분한다. 객체는 중괄호{}로 감싸서 표시한다.&#x20;
* 자바스크립트 객체 문법으로 되어있기 때문에 자바스크립트와의 호환성이 아주 좋다.&#x20;
  * JSON.parse(json\_data)

## 타입

* 문자열, 숫자, boolean, object, 배열, null&#x20;

```json
//aboutme.json
{
  "name" : "halmuni",
  "age" : 20,
  "favorites" : ["sleeping", "coffee", "coding", "running"],
  "is_married" : false,
  "family" : [
    {
      "relation" : "mother",
      "name" : "kim ok ja"
    },
    {
      "relation" : "father",
      "name" : "hong gil dong"
    },
    {
      "relation" : "sister",
      "name" : "hong duli"
    },
    {
      "relation" : "brother",
      "name" : "hong ttochi"
    }
  ]
}
```



## 참조방법

* 위의 데이터를 참조한다고 가정해보자.&#x20;
* aboutme.json 을 javascript 객체로 파싱한 뒤,&#x20;
* aboutme.js 의 이름, 나이, 좋아하는 것 중 처음의 두 가지 요소, 결혼여부, 그리고 가족 중 엄마의 이름을 참조해보자.&#x20;

```javascript
//aboutme.js
const me = {
  "name" : "halmuni",
  "age" : 20,
  "favorites" : ["sleeping", "coffee", "coding", "running"],
  "is_married" : false,
  "family" : [
    {
      "relation" : "mother",
      "name" : "kim ok ja"
    },
    {
      "relation" : "father",
      "name" : "hong gil dong"
    },
    {
      "relation" : "sister",
      "name" : "hong duli"
    },
    {
      "relation" : "brother",
      "name" : "hong ttochi"
    }
  ]
}

//aboutme.json 참조하기 
console.log(me.name)
console.log(me.age)
console.log(me.favorites[0], me.favorites[1])
console.log(me.is_married)
console.log(me.family[0].name)
console.log(me.family[0]["name"])
```

## 주의할 점&#x20;

1. 오직 key-value 만 담을 수 있다. undefine나 매서드는 담을 수 없다.&#x20;
2. 큰 따옴표만을 사용해야한다.&#x20;

## 장점

1. 텍스트로 이루어져 있어서, 사람과 컴퓨터가 모두 읽고 쓰기가 쉽다.&#x20;
2. 프로그래밍 언어와 플랫폼에 독립적이기 때문에 서로 다른 시스템 간에 객체를 교환하기에 좋다.&#x20;
   1. 프론트에서 javascript 를 쓰고, 백엔드에서 java 를 써도, json 타입으로 쉽게 상호 교환이 가능하다.
   2. 때문에 주로 API나 config 파일에 활용된다.&#x20;
3. 가볍다. 계속 닫힌 태그가 들어가는 xml 과 비교하여 가볍다.&#x20;
