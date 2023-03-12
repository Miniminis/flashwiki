---
description: User Driven Developer, 사용자를 기반으로 성장하는 개발자
cover: .gitbook/assets/marigold.jpg
coverY: -363
---

# 📙 손민희 (MINHEE SON)

## <mark style="color:orange;">Contact</mark>

* [minhee4735@gmail.com](mailto:minhee4735@gmail.com)
* [Blog](https://wiki.mhson.world/) | [Github](https://github.com/miniminis) | [LinkedIn](https://www.linkedin.com/in/minhee-son-3b2a1016a) | [Youtube](https://www.youtube.com/@devgrandma) | [Brunch](https://brunch.co.kr/@nomadflash) &#x20;

***

## <mark style="color:orange;">Introduce</mark>

370일동안 지구 반대편 아프리카 케냐에서 지역개발사업의 Project Manager로 일을 하다가 우연한 계기로 개발을 배웠습니다. 지금은 3년차 개발자로 핀테크, 프롭테크 등의 영역에서 두 곳의 스타트업을 거쳐 모바일 앱과 웹 서비스를 개발하고 있습니다. **초기 스타트업의 유일한 서버 개발자로 합류해 기획단계부터 참여했고 시스템 설계부터 개발까지 팀을 기술적으로 서포트한 경험이 있습니다.**&#x20;

**서비스의 사용자가 0명부터 5만명이 될 때까지 약 2년 동안 많은 기술적 문제상황과 어려움을 마주했고, 이를 효과적으로 해결한 경험이 있습니다.** 끊임없는 A/B 테스트를 통해 프로덕트를 개선했고, 이전보다 늘어난 사용자에 대응하기 위해 꾸준히 성능을 향상시켜왔습니다. 반복적인 작업은 자동화를 통해, 비효율적인 프로세스는 소통과 시스템을 통해 개선해왔습니다.&#x20;

**사용자를 기반으로 성장합니다.** "모든 소프트웨어의 의미는 사용자들로부터 사용됨"에 있다고 믿으며 모든 의사결정은 기술 중심이 아닌 사용자를 중심으로 진행합니다. 핵심 가치를 잘 전달하는 편리한 서비스를 지향하며 안정적이고 빠른 서비스를 만들기 위해 노력해왔습니다.

**How 보다는 Why 에 따라 움직입니다.** 코드가 동작할 때에는 왜 그렇게 동작하는지를 이해하고, 특정 기술을 도입할 때에는 유행에 휩쓸리지 않고 왜 써야하는지를 깊게 고민합니다. 기획서를 마주할 때에는 왜 특정 기능을 개발해야하는지, 해결하고자 하는 본질적인 문제가 무엇인지를 생각합니다. 실제로 더 나은 방향으로 아이디어를 제안해 기획 방향을 개선하거나 코드 한 줄 작성하지 않고 프로덕트의 문제를 해결한 경험이 있습니다.&#x20;

***

## <mark style="color:orange;">Work Experience</mark>

### [홀로스탠딩(Holostanding)](https://holostanding.space/) / [KRG Group](https://www.mubang.co.kr/default/)

누적 사용자 55,000명이 이용하고 총 92억원의 보증금을 삭제한 무보증금 원룸 중개서비스 [홀로스탠딩](https://holostanding.space/)을 만드는 프롭테크 스타트업으로 롯데벤처스, 경동인베스트 등으로부터 투자유치&#x20;

#### **Software Developer |** 2021.02-2022.10(1년 8개월)

* 0에서 1을 만든 경험&#x20;
  * 스타트업의 초기 멤버로 합류하여 서비스의 백엔드 시스템을 혼자서 개발. 4개월 동안 Java, Spring Boot, JPA, QueryDSL, MySQL, AWS 등을 사용하여 시스템 인프라 구축, 데이터베이스 설계, API 개발 등의 작업 수행. 목표 작업기한 내에 성공적으로 서비스 런칭 및 1년 3개월간 안정적으로 운영&#x20;
  * 약 10개월간 임차인용 "홀로스탠딩", 중개사용 "파트너스", 임대인용 "공실 지우개 무방", 내부직원용 관리자 페이지까지 총 4개 서비스의 API를 개발
* 성능 향상, 속도 향상, 안정화, 문제해결
  * TDD 를 기반으로 리펙토링하여 전반적인 코드의 품질을 높이고, 장애 가능성을 낮추는데 기여
  * 지도 기반 임대인 매물 등록 서비스 "공실 지우개 무방" 쿼리 조회 속도 400% 개선&#x20;
  * Native query 로 구현되어있던 대시보드의 통계성 쿼리 100% JPA QueryDSL 로 변경하여 장애 사전 예방 &#x20;
  * Grafana K6 를 이용하여 부하테스트 진행하여 병목지점 파악 및 장애 예상 지점 개선&#x20;
  * 데이터 기반 의사결정을 위한 [통합 데이터 대시보드 구축 작업에 참여](work-and-experience/holostanding-krg-group/undefined.md)하여 온/오프라인으로 단절되었던 데이터들을 통합된 하나의 대시보드 상에서 분석할 수 있도록 개선. 대시보드에 사용된 모든 쿼리 작업을 진행
* 비효율적인 프로세스 개선, 반복작업 자동화
  * 비효율적이고 시간소요가 컸던 기존의 서버 배포 프로세스를 [AWS CodePipeline 을 이용하여 CI/CD 자동화](infrastructure/aws/aws-codepipeline-01.md). 전체 배포 시간 약 10분 단축
  * [AWS SES, Aligo SMS API, Spring Batch 등을 이용하여 CRM 프로세스 구축](work-and-experience/holostanding-krg-group/aws-ses-aligo-sms-api-spring-batch-crm.md). 일 평균 3,000개의 SMS 및 카카오 알림톡 전송을 자동화&#x20;
  * [슬랙봇을 이용한 서비스 모니터링 시스템 구축](work-and-experience/holostanding-krg-group/undefined-1.md). 통합된 모니터링 채널을 통해 이슈 대응속도 개선 및 모니터링 업무 효율 개선&#x20;
  * [이슈리포트 작성 문화 도입 및 초기 템플릿을 구축](work-and-experience/holostanding-krg-group/undefined-2.md)하여 이슈 대응 속도 향상 및 커뮤니케이션 비용 절감&#x20;
* 사용자 편의성 향상, 그로스 해킹
  * [전문가 상세페이지 내부 상담하기 버튼을 추가하는 기획을 제안](work-and-experience/holostanding-krg-group/undefined-3.md)하여 목표 단계까지 전환률 향상
  * [Admin 화면 기획에 개선안을 제안](work-and-experience/holostanding-krg-group/admin.md)하여 사용성 향상 및 업무 효율성을 증진

***

### [크레파스(CrePASS)](https://www.crepass.com)

비금융 빅데이터를 기반으로 개인의 신용을 평가하는 대안신용평가 플랫폼을 운영 중인 핀테크 스타트업으로 신한금융투자, 스파크랩파트너스, 팁스, 미래에셋벤처투자 등으로부터 투자 유치

#### **Software Developer |** 2019.11-2021.01(1년 3개월)

* 0에서 1을 만든 경험, 사용자 편의성 향상, 프로덕트 개선
  * Kotlin, Android Studio 를 이용해 입사 3개월만에 1만 명이 사용하는 [P2P 플랫폼 서비스 <청년5.5> 안드로이드 앱](https://play.google.com/store/apps/details?id=com.crepass.p2p\&hl=ko\&gl=US) 리뉴얼 버전을 개발. 향상된 앱 사용성 및 개선된 UI를 통해 사용자 경험을 개선
  * 누적 다운로드 2,000건 이상의 모바일 신용등급 플랫폼 [대안신용평가 서비스 CrePASS SDK](https://m.onestore.co.kr/mobilepoc/apps/appsDetail.omp?prodId=0000738635) 를 유지보수하고 <청년 5.5> 앱 내 연동작업을 진행하여 대출신청완료까지 이탈률을 개선
  * [북서울신협 <북서울신협 대안신용평가>](https://m.onestore.co.kr/mobilepoc/apps/appsDetail.omp?prodId=0000753209) 안드로이드 앱 신규 개발 및 원스토어 릴리즈 작업 진행
  * 팀 내 유일한 안드로이드 개발자로서 사용자의 편의성과 회사의 비즈니스적 임팩트를 함께 추구할 수 있도록 꾸준히 개선안 제안. 펀딩 목록 페이지 내 정렬 기준을 최신순이 아닌 펀딩 임박한 순서로 변경할 것을 제안
* 시스템 이전&#x20;
  * P2P 온라인투자연계법 개정에 따른 인프라 이전작업에 참여. 기존 AWS 상에서 운영되던 서버 약 6개를 [Koscom 금융 클라우드](https://www.koscom.cloud)로 이전. 기존 AWS RDS 로 사용 중이던 데이터베이스를 Koscom 의 Cloud DB for MySQL 로 교체 및 데이터 마이그레이션&#x20;
* 팀 문화 및 문서화&#x20;
  * 서비스의 핵심 모듈인 CrePASS SDK 내부 소스코드를 분석한 내용을 팀 내 최초로 문서화하고 팀원들과 공유하여 모듈 내 로직에 대한 전체적인 이해도 향상에 기여
  * 기존 시스템 인프라 및 서버 환경 구성정보를 문서화하고 팀원들과 공유하여 관리되고 있지 않았던 다수의 서버를 발견해 AWS 비용 절감에 기여
  * 사내 기술스터디 및 영어스터디 참여. 학습을 위한 인터넷 강의 지원제도를 제안해 꾸준히 학습하는 팀 분위기 조성에 기여&#x20;

***

### [사단법인 월드투게더](http://www.worldtogether.or.kr/)

지구촌 이웃의 ‘교육을 통한 자립’을 위해 에티오피아, 케냐, 캄보디아, 미얀마, 베트남 등 개발도상국과 국내에서 활동하는 국제개발협력 NGO. 케냐에서는 약 5년간 가축지원사업을 진행

#### **Project Manager |** 2018.01-2019.03(1년 3개월)

* 프로젝트 매니저로서 아프리카 케냐로 파견되어 370일동안 외교부 산하의 KOICA 와 함께 진행하는 3억원 규모의 [케냐 가축지원 3단계 사업](http://www.worldtogether.or.kr/work-present/regional-development)을 맡아 사업 초기 단계부터 운영
* [KOICA 사업 간담회에서 프로젝트 매니저로서 기관을 대표해 사업 성과 발표](https://www.facebook.com/permalink.php?story\_fbid=1083645098484019\&id=175619232619948)
* 문맹률이 높고 연령대가 높은 지역 주민들의 눈높이에 맞게 기존의 영어 텍스트 중심의 젖소 교육 커리큘럼을 영상과 사진, 오프라인 현장 실습 수업 등 실용적인 형식으로 전면 개편. 전체 수혜자 97% 이상이 만족감을 표현.
* 기존보다 규모가 커진 프로젝트에 맞게 케냐 현지 수의 직원 1명 및 문서 보조 직원 1명을 추가 채용하여 정기 모니터링 및 업무 효율을 개선하고 현지 인력에 디지털 업무 노하우를 전수
* 로컬지역 젖소의 품종 개발을 위해 인공수정업체 ABS와 MOU 체결을 진행하여 시중보다 싼 값으로 고품질의 우량종자를 보급받을 수 있도록 함
* 월 1회 지역 공무원, 마을 유지, 프로젝트 수혜자로 구성된 마을 내 프로젝트 위원회 (Project Committee Meeting) 진행을 총괄하여 자발적인 주민조직을 통한 축산 관련 공동체 구성에 기여
* 기후가 건조해지는 건기에 대비하여 사료수급을 원활히 하기 위해 작물 공동재배용 토지 구매 과정 총괄
* 케냐지부의 NGO 등록과정에 필요한 모든 프로세스를 관할하고 젖소 대출금 상환 계좌의 명의를 개인에서 단체로 변경하여 단체의 신뢰성 강화 및 투명한 운영에 기여
* 젖소사육에 필요한 모든 지원 과정 총괄 (축사건축, 방역도구 배분, 백신접종, 착유물품 배분 등)
* 매월 130여명의 수혜자 모니터링을 통해 대출상환을 관리하고 젖소 데이터 수집 및 모니터링

***

### [컨선월드와이드 한국지부](https://concern.or.kr/)

세계 최극빈 지역의 사람들이 기아와 빈곤에 맞서 싸우며 그들 스스로 삶의 변화를 추구할 수 있도록 돕는 아일랜드계 국제인도주의기구

#### **Communication Dept Intern |** 2017.07-2017.08(2개월)

* 한국지부 최초로 뉴스기사, 컨퍼런스 등 기성매체가 아닌 Youtube 플랫폼을 통한 커뮤니케이션 방법 시도. 사업관련 주제의 유투버들에게 협업 제안
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

### 2022.07 \~ 2022.08 Product Maker 스터디 <[쿨피스](https://disquiet.io/product/%EC%BF%A8%ED%94%BC%EC%8A%A4)>

* Building In Public 정신으로 36명의 사람들과 함께 초기멤버로 참여
* `flash`라는 닉네임으로 [디스콰이엇](https://disquiet.io/product/%EC%BF%A8%ED%94%BC%EC%8A%A4) 플랫폼에서 [타 서비스가 겪고 있는 문제에 대해 고민하고 메이커의 관점에서 해결책을 제시하는 활동](work-and-experience/activity/undefined.md)
* 서비스 개발 전반에 대한 내용을 공부하고 [콘텐츠로 발행](https://disquiet.io/@flash)

### 2022.01 \~ 2022.08 새벽 개발자 모각공 커뮤니티 <[Early Bird Morning Club](https://discord.gg/PyJauKJRgg)>

* 매일 새벽 4시부터 8시까지 모여서 각자 개발공부를 하는 온라인 개발자 모각공 커뮤니티 리딩
* 디스코드 서버 누적 참여자 수 45명
* 혼자서 기획, 제작, 모집까지 하여 활발히 운영했으나 2022년 8월을 기점으로 운영종료



## <mark style="color:orange;">Skill</mark>

* Back-End
  * Java, SpringBoot, JPA, QueryDSL
  * AWS(EC2, RDS, S3, Route53, CodePipeline 등)
* Database
  * MySQL
* Communication and Co-working
  * Github, Bitbucket
  * Slack, Notion



## <mark style="color:orange;">Education</mark>

* 2022.09 - 2022.10 NextStep Infra 공방 수강&#x20;
* 2021.04 - 2021.05 NextStep TDD with Java 수강&#x20;
* 2020.12 - 2021.01 Fast campus Java 웹 개발 마스터 올인원 패키지 Online 수강&#x20;
* 2019.04 - 2019.11 Bitcamp, 풀스택 자바 웹 개발자 과정 수료
* 2015.01 - 2015.12 Queens College, ESL(English as a Second Language) Course 수료
* 2013.03 - 2019.08 이화여자대학교, 사학 및 국제학 학사&#x20;



## <mark style="color:orange;">Language</mark>

* English : 글을 읽고 작성할 수 있고 일상적 소통 가능 (TOEIC 975점, IELTS 7.0)&#x20;
* Chinese : HSK 5급까지 도전했으나 사용한지 오래됨&#x20;
* Kiswahili : 케냐에 거주하는 동안 간단한 인사와 소개말 정도를 배움&#x20;

