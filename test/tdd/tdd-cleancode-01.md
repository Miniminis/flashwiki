---
title: <TDD Clean Code with Java> TDD 기본개념/장점/핵심/예시/연습
category: tdd
date: 2021-10-29 20:47:37
---


[지난 포스팅](https://blog.mhson.world/2021/10/29/comeback-to-tdd/)에서 말했듯, [Nextstep의 자바 클린코드 TDD 과정](https://edu.nextstep.camp/c/8fWRxNWU/)을 듣고 있다. 과정은 미션을 수행하고 이에 대해 코드 리뷰를 받으며 티키타카, 핑퐁의 과정을 거치면서 진행되지만, TDD 핵심 개념이나 적용방법과 같은 내용들은 강의를 통해 자세히 설명을 해주시는데, 오늘은 기억에 남는 몇 가지 구절들을 정리해보려고 한다. 


## 우선, TDD란
- 테스트 주도 개발 (Test Driven Development)
- 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술로서
- 사실 테스트를 하기 위한 기술이라기 보다는 분석기술이며, 설계기술이다. 
- TDD 로 개발을 진행하기 위해서는 요구사항 분석이 필요적이다.

## 기본개념
TDD 의 사이클은 아래와 같이 진행된다. 
1. 실패하는 테스트를 만든다. 
2. 테스트 통과하는 기능을 만들고 
3. 리펙토링을 한다 

나는 1번과 2번을 하다가 대게 3번을 누락하게 되는 것 같은데, 3번이 사실상 가장 필수적이다.

## 도대체 어떤 점이 좋은가
#### 변경에 대한 영향도
- TDD로 구현을 진행하게 되면 비즈니스 요구사항이 추가나 변경되더라도 테스트 통과여부로 대부분의 소스코드를 점검할 수 있으니, 변경사항에 대한 영향도를 바로 체크해볼 수 있고, 그에 따라 안정적으로 서비스를 운영할 수 있다. 

#### 한번에 한가지만 집중
- 보통은 한 프로젝트를 진행하면 큰 단위를 한꺼번에 생각하게 되니까 여러개의 로직들을 한번에 생각하는 경향이 있게 된다. (매우 공감갔던 부분)
- 하지만 TDD 에서는 기능 구현에만 집중하면 되므로, 한번에 한 가지 테스트 통과를 위해 한 가지 기능 구현에만 집중할 수 있게 된다. 
- 기능이 일단 구현되면 사람은 심리적 안정이 생긴다.
  - `이 안정감이 생길때부터가 바로 리펙토링 설계에서 창의성이 발휘될 수 있는 부분!!!`
- 따라서 기능을 구현할때에는 기능 이외에 네이밍이나 코드같은 부분을 고민하지 말고 기능구현을 우선 완료한 다음에
- 심리적인 안정감을 가지고 리펙토링 과정을 시작한다. 이 과정에서 창의성이 나온다. 

이 부분은 정말로 공감이 많이 갔던 부분이다. 실제로 개발을 하면서 내가 느꼈고 느끼고 있는 부분이기도 하기에.

#### 내가 짠 코드에 대한 자부심
- 프로그래머로 일하면서 이왕이면 전문가로서 자부심을 느끼면서 살아가자
- 개발자라는 포지션으로 일을 하면서 나는 제대로 일하면서 살아가고 있을까?
- 심지어는 너무 바빠서 7시간동안 쓰레기 코드를 싸질러도 1시간만 리펙토링을 하는 것만으로도 만족하면서 일을 해나갈 수 있다. 

절실하게 느끼는 부분이다. 예전에 어떤 개발자 분의 책을 읽었었는데, 개발자라고 적혀있는 본인의 명함을 보면서 스스로가 떳떳하게 개발자라고 자랑스럼게 말할 수 있게 되기까지 3년 이상이 걸렸었다고 했다. 
나도 개발자로 일을 하며 친구들을 만나지만, 친구들이 멋지고 대단하다고 말해줄때마다 `난 정말 친구들 앞에서 나름 꽤 쓸만하고 멋진 코드를 적는, 멋진 서비스를 만드는 사람이라고 말할 수 있을까` 스스로에게 되물어보게 된다. 아직 너무나도 부족한게 많다고 느껴진다.

## 결국 핵심은
1. 큰 단위를 작은 단위로 나눠서 빠르게 실패한다. 
2. 피드백을 통해서 지속적으로 개선한다. 
3. 달성하기 힘들 것으로 생각하는 일에 도전할 수 있는 용기를 얻는다. 

큰 성공으로 가는 길 = 여러번의 작은 실패 -> 여러 번의 작은 성공 -> 큰 설공

## 예를들면 - 삶에 적용하기
#### 프로그래밍 = 글쓰기 
- 기능을 구현한다 = 초고
- 리펙토링 = 퇴고

#### TDD 와 책쓰기 
1. 우선 현재상태에서 쓸 수 있는 내용 위주로 빠르게 챕털르 마무리 한다. 
2. 챕터를 마무리 했으므로 이너피스가 생성된다. 
3. 챕터를 마무리한 후 편집자, 주변 친구들에게 공유해서 피드백을 받는다.
4. 피드백을 받아서 챕터별 퇴고를 한다. 
5. 모든 챕터를 마무리하고 2차 퇴고를 한다.
6. `마음에 들때까지 무한 루프!`

#### TDD 와 창업하기
1. 측정가능한 가설을 세운다 (테스트코드)
2. 구현을 통해서 소비자에게 피드백을 건넨다. 
3. 개선한다. 
4. 목표수익을 달성할때까지 반복개선한다.
5. 린스타트업이 된드아! 

## 그렇다면 어떻게 연습할까 - 의식적으로 연습하기
#### 의식적인 연습의 7가지 원칙
1. 효과적인 훈련 기법이 수립되어있는 기술을 연마한다. 
2. 개인의 컴포트 존을 벗어난 지점이서 진행한다. 자신의 현재 능력을 살짝 넘어가는 작업을 지속적으로 시도한다. 
3. 명확하고 구체적인 목표를 가지고 진행한다. 
4. 신중하고 계획적이다. 즉 개인이 온전히 집중하고 의식적으로 행동할 것을 요구한다. 
5. 피드백과 피드백에 따른 행동 변경을 수반한다. 
6. 효과적인 심적 표상을 만들어내는 한편, 심적 표상에 의존한다. 
7. 기존에 습득한 기술의 특정 부분을 집중적으로 개선함으로써 발전시키고, 수정하는 과정을 수반한다. 

하나 같이 구구절절 맞는 말들이라는 생각이 든다. 
`무조건 많이 하는게 아니라 컴포트존을 살짝씩 넘으면서 연습을 할때 실력이 는다는 것이다. `

평생동안 연습하겠다는 마음가짐으로 시작해야한다. 
-> 좋은 코드를 평생 만들겠다는 마음가짐! 

자바지기님은 5-6년을 도전한 후에야 
- 테스트하기 쉬운코드와 테스트하기 어려운 코드를 보는 눈과
- 테스트하기 어려운 코드를 하기 쉬운 코드로 설계하는 감이
생겼다고 한다. 

**`결국 나의 컴포트존을 조금씩 넘기는 꾸준한 노력만이 답이다.`**