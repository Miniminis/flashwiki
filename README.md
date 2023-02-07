---
description: 본질을 파악하여 정면으로 맞서는 개발자
---

# 🧙 손민희

## <mark style="color:orange;">Contact</mark>

* [minhee4735@gmail.com](mailto:minhee4735@gmail.com)
* [Blog](https://wiki.mhson.world/) | [Github](https://github.com/miniminis) | [LinkedIn](https://www.linkedin.com/in/minhee-son-3b2a1016a) | Twitter | Instagram | Youtube&#x20;

***

## <mark style="color:orange;">Introduce</mark>

370일동안 [지구 반대편 아프리카 케냐에서 지역개발사업의 Project Manager로 일을 하다가](https://brunch.co.kr/magazine/africarurallife) 우연한 계기로 개발을 배웠습니다. 현재는 3년차 개발자로 핀테크, 프롭테크 등의 영역에서 두 곳의 스타트업을 거쳐 모바일 앱, 웹 서비스를 개발, 배포, 운영해왔습니다. 초기 스타트업의 유일한 서버개발자로 합류하여 프로덕트의 설계부터 개발, 배포 및 운영까지 팀을 기술적으로 서포트한 경험이 있습니다.

항상 능력보다 어려운 과제에 도전하여 의미있는 결과들을 만들어냈습니다. 전혀 다른 분야나 모르는 것에 대한 두려움이 없어 빠르게 도전하고 빠르게 실패하며 J커브 성장을 해냅니다. 비 IT직군에서 빠르게 개발자로 전향을 한 경험이 있고, 익숙하지 않은 기술들을 빠른 속도로 학습하며 프로덕트를 만들어낸 경험을 해왔습니다.

문제의 본질을 찾는 것을 중요시합니다. 본질을 알면 가장 적은 비용으로 가장 빠른 지름길을 찾을 수 있다고 믿습니다. 때문에 데이터를 보거나 개발요청을 받을때도 끊임없이 질문하려고 노력하고 있습니다. 실제로 기획자나 운영팀의 개발 요청 건에 대해 문제 해결을 위한 더 나은 방법을 제안해 수용된 경험이 있습니다.

***

## <mark style="color:orange;">Work Experience</mark>

### [홀로스탠딩(Holostanding)](https://holostanding.space/)

누적 사용자 42,000명이 이용하는 무보증금 원룸 중개서비스 [홀로스탠딩](https://holostanding.space/)을 만드는 프롭테크 스타트업으로 롯데벤처스, 경동인베스트 등으로부터 투자유치

#### **Software Developer |** 2021.02-2022.10

* **스타트업의 초기 멤버로 합류하여 서비스 백엔드 개발을 혼자서 담당.** 4개월 동안 Java, Spring Boot, JPA, QueryDSL, MySQL, AWS 등을 사용하여 시스템 인프라 구축, 서버 초기 환경설정, 데이터베이스 설계, API 개발 및 배포 등의 작업 수행
  * **목표 작업기한 내에 성공적으로 서비스 런칭 및 1년 6개월간 안정적으로 운영**
  * 약 10개월간 임차인용 홀로스탠딩, 중개사용 파트너스, 임대인용 공실지우개, 내부직원용 백오피스까지 총 4개 서비스의 API를 처음부터 개발
* **서비스 런칭 직후, TDD를 기반으로 전체 서버 코드를 리펙토링 하여 전반적인 코드의 품질을 높이고, 반복작업을 줄여 업무의 효율화를 이끌어냄**
* [AWS SES, Aligo SMS API, Spring Batch 등을 이용하여 CRM 프로세스 구축](work-and-experience/holostanding/aws-ses-aligo-sms-api-spring-batch-crm.md). 일 평균 2,000개의 SMS 및 카카오 알림톡 전송 자동화
* 비효율적이고 시간소요가 컸던 기존의 서버 배포 프로세스를 [AWS CodePipeline 을 이용하여 CI/CD 자동화](infrastructure/aws/aws-codepipeline-01.md). 전체 배포 시간 약 5분 단축
* [슬랙봇을 이용한 서비스 모니터링 시스템 구축](work-and-experience/holostanding/undefined-4.md)(SMS 전송 서비스의 잔여금액, AWS EC2 서버상태 및 배치 프로세스 결과 등). 통합된 모니터링 채널을 통해 이슈 대응속도 개선 및 모니터링 업무 로드 줄임
* 팀원들과 함께 [온오프라인 데이터 대시보드 구축작업 진행](work-and-experience/holostanding/undefined-2.md)(필요한 지표 정의 및 데이터 조회를 위한 쿼리작업 등). 기존의 온라인, 오프라인으로 단절되었던 데이터를 통합된 대시보드 상에서 관리하도록 개선
  * **쿼리 튜닝 및 개선으로 기존 00 시간에서 00시간으로 속도 단축**
* 팀 내 유일한 서버 개발자로서 서비스 운영 과정에서 발생하는 각종 이슈 및 장애에 신속히 대응하여 서비스를 안정적으로 운영하는데 기여

***

### [크레파스(CrePASS)](https://www.crepass.com)

비금융 빅데이터를 기반으로 개인의 신용을 평가하는 대안신용평가 플랫폼을 운영하는 핀테크 스타트업으로 신한금융투자, 스파크랩파트너스, 팁스, 미래에셋벤처투자 등으로부터 투자 유치

#### **Software Developer |** 2019.11-2021.01

* **Kotlin, Android Studio 를 이용해 입사 3개월만에** [**P2P 플랫폼 서비스 <청년5.5> 안드로이드 앱**](https://play.google.com/store/apps/details?id=com.crepass.p2p\&hl=ko\&gl=US) **리뉴얼을 위한 개발 및 앱스토어 배포를 진행. 향상된 앱 사용성 및 개선된 UI를 통해 사용자 경험 개선**
* **누적 다운로드 2,000건 이상의 모바일 신용등급 플랫폼** [**대안신용평가 서비스 CrePASS SDK**](https://m.onestore.co.kr/mobilepoc/apps/appsDetail.omp?prodId=0000738635) **를 유지보수하며 <청년 5.5> 앱 내 연동작업 진행하여 대출신청완료까지 이탈률 개선**
* [북서울신협 <북서울신협 대안신용평가>](https://m.onestore.co.kr/mobilepoc/apps/appsDetail.omp?prodId=0000753209) 안드로이드 앱 신규 개발 및 원스토어 릴리즈 작업 진행하여 신용정보가 부족한 대출신청자에게 대안적인 신용평가 창구 제공
* **P2P 온라인투자연계법 개정에 따른 인프라 이전작업에 참여**
  * 기존 AWS 상에서 운영되던 서버 약 6개를 Koscom 금융 클라우드로 이전
  * 기존 AWS RDS 로 사용 중이던 데이터베이스를 Koscom 의 Cloud DB for MySQL 로 교체 및 데이터 마이그레이션
* 서비스의 핵심 모듈인 CrePASS SDK 내부 소스코드를 분석한 내용을 팀 내 최초로 문서화하고 팀원들과 공유하여 모듈 내 로직에 대한 전체적인 이해도 향상에 기여
* 기존 시스템 인프라 및 서버 환경 구성정보를 문서화하고 팀원들과 공유하여 관리되고 있지 않았던 다수의 서버를 발견해 AWS 비용 절감에 기여
* 팀 내 유일한 프론트 개발자로서 사용자의 편의성과 회사의 비즈니스적 임팩트를 함께 추구할 수 있도록 꾸준히 개선안 제안
  * 펀딩 목록 페이지 내 정렬 기준을 최신순이 아닌 펀딩 임박한 순서로 변경 제안
* 사내 기술스터디 및 영어스터디 참여. 학습을 위한 인터넷 강의 지원제도를 도입에 앞장서 꾸준히 학습하는 팀 분위기 조성에 기여

***

### [사단법인 월드투게더](http://www.worldtogether.or.kr/)

지구촌 이웃의 ‘교육을 통한 자립’을 위해 에티오피아, 케냐, 캄보디아, 미얀마, 베트남 등 개발도상국과 국내에서 활동하는 국제개발협력 NGO. 케냐에서는 약 5년간 가축지원사업을 진행

#### **Project Manager |** 2018.01-2019.03

* 프로젝트 매니저로서 아프리카 케냐로 파견되어 370일동안 외교부 산하의 KOICA 와 함께 진행하는 3억원 규모의 [케냐 가축지원 3단계 사업](http://www.worldtogether.or.kr/work-present/regional-development)을 맡아 사업 초기 단계부터 운영
* [KOICA 사업 간담회에서 프로젝트 매니저로서 기관을 대표해 사업 성과 발표](https://www.facebook.com/permalink.php?story\_fbid=1083645098484019\&id=175619232619948)
* 문맹률이 높고 연령대가 높은 지역 주민들의 눈높이에 맞게 기존의 영어 텍스트 중심의 젖소 교육 커리큘럼을 영상과 사진, 오프라인 현장 실습 수업 등 실용적인 형식으로 전면 개편. 전체 수혜자 97% 이상이 만족감을 표현.
* 기존보다 규모가 커진 프로젝트에 맞게 케냐 현지 수의 직원 1명 및 문서 보조 직원 1명을 추가 채용하여 정기 모니터링 및 업무 효율을 개선하고 현지 인력에 디지털 업무 노하우를 전수
* 로컬지역 젖소의 품종 개발을 위해 인공수정업체 ABS와 MOU 체결을 진행하여 시중보다 싼 값으로 고품질의 우량종자를 보급받을 수 있도록 함
* 월 1회 지역 공무원, 마을 유지, 프로젝트 수혜자로 구성된 마을 내 프로젝트 위원회 (Project Committee Meeting) 진행을 총괄하여 자발적인 주민조직을 통한 축산 관련 공동체 구성에 기여
* 기후가 건조해지는 건기에 대비하여 사료수급을 원활히 하기 위해 작물 공동재배용 토지 구매 과정 총괄
* 케냐지부의 NGO 등록과정에 필요한 모든 프로세스를 관할하고 젖소 대출금 상환 계좌의 명의를 개인에서 단체로 변경하여 단체의 신뢰성 강화 및 투명한 운영에 기여
* 젖소사육에 필요한 모든 지원 과정 총괄
  * 축사건축, 방역도구 배분, 백신접종, 착유물품 배분 등
* 매월 130여명의 수혜자 모니터링을 통해 대출상환을 관리하고 젖소 데이터 수집 및 모니터링

***

### [컨선월드와이드 한국지부](https://concern.or.kr/)

세계 최극빈 지역의 사람들이 기아와 빈곤에 맞서 싸우며 그들 스스로 삶의 변화를 추구할 수 있도록 돕는 아일랜드계 국제인도주의기구

#### **Communication Dept Intern |** 2017.07-2017.08

* 한국지부 최초로 뉴스기사, 컨퍼런스 등 기성매체가 아닌 Youtube 플랫폼을 통한 커뮤니케이션 방법 시도
  * 사업관련 주제의 유투버와 협업 제안
* 2014년 아프리카에 에볼라 질병 확산 당시 컨선월드와이드가 개발한 <[**존엄하고 안전한 매장(Safe and dignified Burials Programme)**](https://concern.or.kr/172)> 온라인 콘텐츠 제작 및 공식 홈페이지에 개재
* 기관과 관련된 뉴스 데일리 모니터링 후 한국지부 전사 공유 작업 진행하여 매일 팀원들과 인사이트 공유
* 매년 컨선월드와이드와 독일의 세계기아원조(Welthungerhilfe)가 함께 공동으로 발표하는 **세계기아지수** (Global Hunger Index) 및 세계기아지수리포트 관련 리서치 작업 진행

***

## <mark style="color:orange;">Side Project</mark>

### [서울 빵 맛집 잘알 테스트](https://github.com/Miniminis/arirang-first-project-front)

사내에서 팀원 중 한명이 자칭 ‘빵 전문가’라고 지칭하여 논란이 된 현상을 지켜보며 빵집 전문가 인증서를 발급해주는 테스트를 제작하여 사내에 공유. 해당 팀원의 경우 최상등급인 “빵지순례좌” 인증서를 발급받아 논란이 종식됨

#### Project Planning & Development **|** 2021.08 - 2021.09

* 동료 마케터와 같이 기획하고 홀로 Javascript, React, Styled Component, AWS Amplify 를 빠르게 학습하여 프론트엔드 웹 페이지를, Java와 Spring Boot, Mysql, AWS CodePipeline 을 이용하여 서버를 개발
* 테스트에 등장하는 빵집 40여곳에 인스타 DM 전송하고 직장인이 가장 피곤한 점심시간 직후에 테스트 링크를 카톡을 통해 전파하여 **총 2,200명의 방문자를 만났고 2,069명의 참여를 이끌어냄**

***

## <mark style="color:orange;">Activity</mark>

### Youtube <[이상한 나라의 개발자 할무니](https://www.youtube.com/@devgrandma)> 채널 운영

* 독특한 서비스나 재미있는 개발 프로젝트를 소개하고 직접 개발하는 과정을 담아 "**위대하고 대단한 서비스를 만들지 않더라도 여전히 IT 서비스를 만드는 즐거움을 느낄 수 있다**"는 메시지를 전하고 있음



## <mark style="color:orange;">Community</mark>

### 2022.02 \~ (현재) 국제개발협력 청년 플랫폼 <[공적인 사적모임](https://www.notion.so/a498308da70c49a88046538e9cacc512)>

* **조직 내 IT 팀을 신설하여 비영리분야를 위한 프로덕트 개발, 노코드 툴 교육 등 다양한 IT 프로젝트를 진행중**
* 국제개발협력 뉴스레터 [김치앤칩스](https://page.stibee.com/archives/76249)의 필진으로 참여
  * <[NGO는 아니지만 국제개발협력합니다](https://www.notion.so/0d9f5f4719234807a128d5bde229809b)> 인터뷰 시리즈 기획 및 제작하여 2022년 한해동안 총 13명의 인터뷰 진행

### 2022.07 \~ 2022.08 프로덕트 메이커 스터디 <[쿨피스](https://disquiet.io/product/%EC%BF%A8%ED%94%BC%EC%8A%A4)>

* Building In Public 정신으로 36명의 사람들과 함께 초기멤버로 참여
* `flash`라는 닉네임으로 [디스콰이엇](https://disquiet.io/product/%EC%BF%A8%ED%94%BC%EC%8A%A4) 플랫폼에 올라온 [서비스의 문제에 대해 고민하고 메이커의 관점에서 해결책을 제시](broken-reference)하는 활동
* product making 전반에 대한 내용을 공부하고 [콘텐츠로 발행](https://disquiet.io/@flash)



### 2022.01 \~ 2022.08 새벽 개발자 모각공 커뮤니티 <[Early Bird Morning Club](https://discord.gg/PyJauKJRgg)>

* 매일 새벽 4시부터 8시까지 모여서 각자 개발공부를 하는 온라인 개발자 모각공 커뮤니티 리딩
* 디스코드 서버 누적 참여자 수 45명
* 혼자서 기획, 제작, 모집까지 하여 활발히 운영했으나 2022년 8월을 기점으로 운영종료



## <mark style="color:orange;">Skill</mark>

* Back-End
  * Java, SpringBoot, JPA, QueryDSL
* Infrastructure
  * AWS(EC2, RDS, S3, Route53, CodePipeline)
* Database
  * MySQL
* Communication and Co-working
  * Github, Slack, Notion



## <mark style="color:orange;">Language</mark>

* English : 글을 읽고 작성할 수 있고 일상적 소통 가능 (TOEIC 975점, IELTS 7.0)
* Chinese : HSK 5급까지 시험봤지만 사용해 본 지는 오래됨
* Kiswahili : 케냐에 거주하는 동안 간단한 인사와 소개말 정도를 배움



## <mark style="color:orange;">Education</mark>

* 2022.09 - 2022.10 NextStep Infra 공방
* 2021.04 - 2021.05 NextStep TDD with Java
* 2019.04 - 2019.11 Bitcamp, 풀스택 자바 웹 개발자 과정 수료
* 2015.01 - 2015.12 Queens College, ESL(English as a Second Language) Course 수료
* 2013.03 - 2019.08 이화여자대학교, 사학 및 국제학 학사
