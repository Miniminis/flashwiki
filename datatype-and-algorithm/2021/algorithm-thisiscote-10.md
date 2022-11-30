---
title: <이코테 2021> 10. 개발형 코딩테스트
category: algorithm
date: 2022-02-09 05:44:25
---

> 💡 동빈나 님의 [이코테 2021 강의 몰아보기](https://www.youtube.com/watch?v=m-9pAwq1o3w&list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&index=1) 를 보면서 공부한 내용을 정리하고 있습니다. 더 자세한 내용은 **[이것이 취업을 위한 코딩 테스트다 with 파이썬](http://www.yes24.com/Product/goods/93519145) 을 참고해주세요** 😊 학습 도구로는 [리플렛](https://replit.com/) 을 사용하고 있고 원본 소스코드는 동빈님의 [Github](https://github.com/ndb796/python-for-coding-test) 에서 확인할 수 있고  스스로 공부한 소스코드는 [Github](https://github.com/Miniminis/algorithm-study-note-python) 에서 확인할 수 있습니다.

## 개발형 코딩 테스트
- 정해진 목적에 따라서 동작하는 완성된 프로그램을 개발하는 것을 요구하는 유형
- 알고리즘 코딩 테스트
  - 요구 사항에 맞는 하나의 모듈을 개발
    - 시간 복잡도 분석
    - 공간 복잡도 분석
- 개발형 코딩 테스트 
  - 완성도 높은 하나의 프로그램을 개발
  - 모듈을 적절히 조합하는 능력을 요구
- 해커톤을 통해서 채용을 진행하기도 한다. 
  - 해커톤 : 단기간에 아이디어를 제품화 하는 프로젝트 이벤트
  - 대게 1-2일정도 진행된다. 
  - 다수의 해커톤이 대회 형식을 빌려서 해커톤 종료 후 프로그램을 시연하고 발표한 다음 채점을 진행한다. 
- 분야에 따라서 상세 요구사항이 다를 수 있다. 
  - 모바일 클라이언트 앱
  - 웹 서버 개발 
- 분야 상관없는 개념과 도구
  - 서버, 클라이언트, JSON, REST API 


## 서버와 클라이언트 
> [자세한 설명 보기](https://youtu.be/d8KPN79UAKA?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=132)

- 클라이언트가 요청을 하면 request
- 서버는 응답을 한다 response

## HTTP 
- HTTP : hyper text transfer protocol
  - 웹 상에서 데이터를 주고 받기 위한 프로토콜
  - 모바일 앱 및 게임 개발 등에서 특정 형식의 데이터를 주고 받는 용도로도 사용한다. 
- 클라이언트는 요청의 목적에 따라서 적절한 http 매서드를 이용해 통신을 진행한다. 
  - 대표적인 매서드는 다음과 같다. 
    - GET
    - POST
    - PUT
    - DELETE

```python
import requests

target = "https://google.com"
response = requests.get(url=target)
print(response.text)
```


## 개발형 코딩 테스트 준비하기
2020 카카오 2차 코딩테스트 안내문 문장
> 오프라인 코딩 테스트에서는 JSON format 의 데이터를 응답하는 REST API 를 활용해야하니, REST API 호출과 JSON format 데이터를 파싱해 활용할 수 있는 parser 코드를 미리 준비해오시길 바랍니다. 

핵심 키워드는 REST API, JSON

### REST
- HTTP 는 GET, POST, PUT, DELETE 등 다양한 매서드를 지원한다.
- 하지만 실제로 서버가 각 매서드의 기본 설명을 따르지 않아도 프로그램을 개발하는데는 전혀 문제되지 않는다. 
  - 우리 서버에서는 DELETE 매서드로 사용자를 추가할거야 
- 이렇게되면 저마다의 방식으로 개발하게 되어 문제가 될 수 있다. 표준이되는 아키텍처가 필요하다. 
- 구성요소
  - 자원 : URL 를 이용 
  - 행위 : HTTP method 이용
  - 표현 : 페이로드를 이용
- Http 패킷 정보
  - URI : https://example.com/users
  - HTTP method : POST
  - payload : {"id" : "gildong1234", "password" : "password1234"}

### REST API 
- Rest architecture 를 따르고 있는 API 
- rest api 호출 : rest 방식을 따르고 있는 서버에 특정한 요청을 전송하는 것

### JSON
- 데이터를 주고 받기 위해서 사용하는 경량의 데이터 형식
- 키와 쌍으로 이루어졌다. 
- python 의 사전 자료형과 매우 유사하다. 


```python
import json

user = {
  "id" : "gildong",
  "password" : "1234",
  "age" : 30
}

json_data = json.dumps(user, indent=4)
print(json_data)

# {
#     "id": "gildong",
#     "password": "1234",
#     "age": 30
# }
```

### JSON file

```python
import json 


user = {
  "id" : "gildong",
  "password" : "1234",
  "age" : 30
}

with open("user.json", "w", encoding="utf-8") as file:
  json_data = json.dump(user, file, indent=4)
```

## REST API 연습용 서비스
- 목킹 : 어떤 기능이 있는것처럼 흉내내어 구현한 것을 의미
- 가상의 REST API 제공 서비스
  - json placeholder
  