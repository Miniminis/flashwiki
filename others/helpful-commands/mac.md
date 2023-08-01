# Mac 에서 특정 포트 검색, 종료

* 현재 열린 포트 목록 확인: `sudo lsof -PiTCP -sTCP:LISTEN`
* 특정 포트를 찾아 종료하고 싶을 때
  * 특정 포트의 PID 찾기: `sudo lsof -i :8080`
  * 위에서 구한 PID로 종료: `sudo kill -9 {PID}`

## 참고&#x20;

* [https://pangtrue.tistory.com/348](https://pangtrue.tistory.com/348)
