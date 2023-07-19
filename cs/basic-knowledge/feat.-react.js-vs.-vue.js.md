# 신기술 도입시 고민해야할 점(feat. react.js vs. vue.js)

* 새로운 기술을 팀에 도입할 때에는 항상 다양한 측면들을 고민해야한다.
* 고민하는 요소들 중에는 다음과 같은 것들이 있다.&#x20;
  * 자유도, 타 플러그인 의존도, 공식 문서 퀄리티, 러닝커브, 성능, 커뮤니티, 모바일로의 확장성, 팀원들의 역량 등



## Vue.js vs React.js

| Vue                                                                                                | React                                                                                              |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| <p>반응형<br>- watched, computed 가 있으며, 값 대입만으로 수정이 가능하다. </p>                                        | <p>함수형<br>- immutable, setState 등 함수를 통해서 값을 바꿀 수 있다. <br>- mapStatetoProps, pureComponent </p>    |
| 양방향 바인딩                                                                                            | 단방향 바인딩                                                                                            |
| html                                                                                               | JSX                                                                                                |
| 자유도 낮음                                                                                             | 자유도 높음                                                                                             |
| 의존도 낮음                                                                                             | 의존도 높음                                                                                             |
| Documentation 쉬움                                                                                   | Documentation 보통                                                                                   |
| 러닝커브 쉬움                                                                                            | 러닝커브 보통                                                                                            |
| [성능 좋음](https://krausest.github.io/js-framework-benchmark/2022/table\_chrome\_105.0.5195.102.html) | [성능 좋음](https://krausest.github.io/js-framework-benchmark/2022/table\_chrome\_105.0.5195.102.html) |
| 커뮤니티 열세                                                                                            | 커뮤니티 우세                                                                                            |
| 모바일 확장성 별로 - Native script, Capacitor                                                              | 모바일 확장성 좋음 - ReactNative                                                                           |

