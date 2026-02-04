#  DEV WIKI



**Quick**

[크래프톤 정글](/Project/크래프톤_정글/index.md)



> [!NOTE] Note-taking System 개선 (feat. submodule)
>
> - 위키를 다수 만들고 운영하자. dev-wiki 및 job-wiki에 모두 집어넣는것은 이제 무리다.
> - 그러면서도 동시에 모든 위키를 관리할 수 있는 접근방법을 구성하자.
> - cron-job으로 대외비가 아닌 것들을 recap하자.
>     - 그날의 모든 변경사항 git stage에 올리고, recap 문서 생성
>     - 커밋 메시지로 삼아 커밋
>     - 개별 커밋 메시지를 통합해 root git dir에서 TIL 생성







  

## Project



[크래프톤 정글](/Project/크래프톤_정글/index.md)

[결혼에 확신을 갖는 방법 찾기](/Project/결혼에_확신을_갖는_방법_찾기/index.md)

[만다라트](/Project/만다라트/index.md)
  - 2차전에 돌입했다.
  


> [!td]
>
> [대만 여행](대만_여행) 후기를 10분정도 간단히 작성해보자.  -->  archive



### 프로젝트 아이디어


- 차량 관리
  - 코란도 엔진오일
  - 경비 아저씨가 나 대신 ..
  - 관련해서 스트레스를 준 아저씨가 있다. 골프장 아저씨



- 생활법률상식사전
  : 책인데, 이런거 온라인으로 접할 수 있다면 좋을것 같다.


- 크롬 확장프로그램
  - surfingkey의 경량 버전으로, 일반인도 쉽고 유용하게 사용할 수 있도록. 특히 이미지 다운로드 등
    편리할듯
  - AI가 현재 방문한 웹사이트의 ui를 매번 완전히 바꿔주는것도 괜찮을듯? 광고를 없앤다던지,
    집중하고싶은것에만 집중한다던지, 색상을 관리해준다던지 등



- keepbuffer.nvim :

  나는 gq 또는 qq로 버퍼를 종료시킨다. qq로 종료는 임시로 종료하고, 버퍼에는 그대로 남겨둔다는
  의미이다. 근데 이게 되살리려고 버퍼 리스트를 조회하면 다른 텝에서 관리하던 버퍼들까지 모두
  나타난다.

  이에 아래와같은 플러그인을 간단히 개발해 사용하면 좋겠다.

  - qq로 종료시 nvim-tree 아래 윈도우 및 버퍼 생성하며, 파일명만 표시
  - 해당 탭에서 qq로 종료 시에만 나타나야하고, 다른 텝과는 아무 상관 없음
  - 생성된 윈도우에서 파일명에 커서를 두고 열면 최근 윈도우의 우측에 다시 윈도우 생성


- 스타크래프트 ai 어시스턴트
  게임 프로세스 및 메모리를 분석해서 의사결정 등에 음성으로 도움을 준다.



- 다양한 종류의 전자잉크 제품을 만들고 싶다. 휴대폰이라던지... 

- (multi-modal)AI와 인터렉션 패러다임 개선/제시
- ai를 통해 기존 디지털 생활 중 일부의 인터페이스 패러다임 개선/제시



- 단순 아이디어
  [독서모임](/Resource/프로젝트_아이디어/독서모임)
  [조직문화를 판매한다. 그것을 뒷받침 할 수 있는 system과 합쳐서.](/Resource/프로젝트_아이디어/조직문화를_판매한다._그것을_뒷받침_할_수_있는_system과_합쳐서.)
  [메뉴얼 수행 추적기](/Resource/프로젝트_아이디어/메뉴얼_수행_추적기)
  [유아(6세 이하 초등입학 전)용 동화 읽어주고 각종 질문에 대답해주는 인형](/Resource/프로젝트_아이디어/유아(6세_이하_초등입학_전)용_동화_읽어주고_각종_질문에_대답해주는_인형)
  [정서, 심리 등 과학적 근거를 바탕으로 하는 검사 컨텐츠를 생성형 인공지능으로 서비스하기](/Resource/프로젝트_아이디어/정서,_심리_등_과학적_근거를_바탕으로_하는_검사_컨텐츠를_생성형_인공지능으로_서비스하기)
  ["시끄러운 목소리"가 주목받는 "댓글" 시스템 개선안](/Resource/프로젝트_아이디어/"시끄러운_목소리"가_주목받는_"댓글"_시스템_개선안)


- 구체화된 아이디어
  [anki-diary](/Resource/프로젝트_아이디어/anki-diary)
  [충북프로메이케센터 홈페이지 리뉴얼](/Resource/프로젝트_아이디어/충북프로메이케센터_홈페이지_리뉴얼)


#### TIPS

- 어떤거 만들어볼만한지는 awesome-selfhosted에 적당히 괜찮은 주제들이 있어요

- 게임 아이디어
  [요런 스타일](https://www.youtube.com/watch?v=YPowsippmRM) 좋다.
  [로그라이크류](https://www.youtube.com/watch?v=JFAzdybohUc)도 재밌다.


## Area

### Programming


> [!NOTE]
>
> 프로덕션에 가까운 프로젝트들을 보면서 배우는 것이 중요하다.
> "Fediverse가 프로덕션에 가깝게 되어있는게 많아요. 루비는 마스토돈, nodejs는 미스키, 파이썬은 bookwyrm"


> [!rf]
>
> 
> - backend course
>   [web-course](https://www.boot.dev/tracks/backend)
>   [github](https://github.com/bootdotdev/curriculum)
> 
> - ETC
>   [7 Habits For Effective Text Editing](https://www.youtube.com/watch?v=eX9m3g5J-XA)
>   [양질의 블로그](https://www.joinc.co.kr/w/architecture)
>   [`TS`, `python` 등 다양한 주제 튜토리얼](https://www.squash.io/tutorials/)
>   [The twelve-factor app](https://12factor.net/)
>   [Harvard CS 50 (2023) – Full Computer Science University Course](https://www.youtube.com/watch?v=LfaMVlDaQ24)
>   [포큐 아카데미 - 프로그래밍 기초](https://pocu.academy/ko/Courses)


#### 살아남는 개발자는 무엇이 다른가?


> [!NOTE]
>
> - 시키는 대로 하기 전에 의심해라. 정말로 필요한 과정/단계/방법인가? 똑똑하고
>   신뢰할만한 사람일수록 더더욱.
> - recipe를 존중하되, 본질을 반드시 이해해라.
> - 쉽게 변하지 않는 것들을 집중 학습하자.
>   
>   - ```from.널널한개발자
>     1. 컴퓨터 구조
>     2. 운영체제
>     3. 프로그래밍 언어
>     4. 자료구조/알고리즘
>     5. 컴파일러
>     ```
>     [reference](https://www.youtube.com/watch?v=JdZWGCVLztc)
>     이 영역들이 서로 영향을 미친다. 예를들어 컴퓨터 구조를 알아야 운영체제를 더 깊이
>     이해할 수 있으며, 더 깊은 운영체제에 대한 이해는 또 다시 컴퓨터 구조 외 여러 영역에
>     대한 더 깊은 이해에 도움을 준다. 따라서 한 단계씩 돌파한다기보다 복잡하게 순환하며
>     수차례 회독하는 것이 반드시 필요하다.
>   
>     
>   - ```from.팀노바
>     1. 언어
>     2. 플랫폼 (운영체제 프로그래밍: 안드로이드/ios/윈도우즈/리눅스/macOS 등등)
>        : 운영체제 만들라는 소리는 아니고 해당 플랫폼에서의 어플리케이션을 만들어보면 그 OS의
>        구조에 대해서도 알게 된다는 의미
>     3. 네트워크 프로그래밍 (OSI 7계층, `2.`에서 공부한 플랫폼에서 TCP 채팅 만들어보기)
>     4. 데이터베이스 심화공부 (RDBMS, NoSQL, CAP 이론, PACELC 이론)
>     5. 분산처리
>     6. 데이터 분석
>     7. 인공지능 (연구 분야와 AI서비스 만들기 분야가 나뉨)
>     ```
>     [reference](https://www.youtube.com/watch?v=TdozAowj1ZA&list=PL0sBvfbMvyZjvcQZk6vhUSHWFA4Tt2vfg&index=3)


> [!rf]
>
> [The LinkedIn DPH Framework](https://linkedin.github.io/dph-framework/)
> [지식근로자의 생산성](https://www.joinc.co.kr/w/knowledge-workder-productivity)



[개발자 중 누가 살아 남을까? 생존을 위해 반드시 생각해야 할 점](/Area/Programming/살아남는_개발자는_무엇이_다른가?/개발자_중_누가_살아_남을까?_생존을_위해_반드시_생각해야_할_점)
[개발자가 앞으로 꼭 공부해야하는 분야](/Area/Programming/살아남는_개발자는_무엇이_다른가?/개발자가_앞으로_꼭_공부해야하는_분야)
[일론의 5단계 법칙](/Area/Programming/살아남는_개발자는_무엇이_다른가?/일론의_5단계_법칙)
[일류와 이류의 차이 (반도체 전설 짐켈러)](/Area/Programming/살아남는_개발자는_무엇이_다른가?/일류와_이류의_차이_(반도체_전설_짐켈러))




#### 구독 서비스 관리

| Category     | Service        | Cost           |
|--------------|----------------|----------------|
| Subscription | GitHub Copilot | $100/Y         |
|              | OpenAI GPT     | ~$20/M~ --> quit |
|              | Figma          | ~$15/M~ --> quit |



#### Hardware

[사용해온 노트북](/Area/Programming/Hardware/사용해온_노트북)

[주변기기](/Area/Programming/Hardware/주변기기)

[docking station과 외부 모니터](/Area/Programming/Hardware/docking_station과_외부_모니터)

[노트북 베터리 관리](/Area/Programming/Hardware/노트북_베터리_관리)



> [!NOTE]
>
> - 알파스캔 모니터 I2477FWQ
>   - 어뎁터 공식: 33,000원
>   - 규격: 12V 3.75A (외경:5.5 / 내경:2.5)



#### Note Making

[Job-wiki 작성법](/Area/Programming/Note_Making/Job-wiki_작성법)

> [!INFO]
>
> [vim-wiki cheet-sheet](https://gist.github.com/drkarl/4c503bccb62558dc85e8b1bc0f29e9cb)
> [design reference](https://mkaz.blog/)
> - 현재 디렉토리 내의 파일명에 공백이 있을 때, 공백을 '_'(under-bar)로 변경하기
>   ```bash
>   for file in *\ *; do mv "$file" "${file// /_}"; done
>   ```


#### Remote Work

[file share](/Area/Programming/Remote_Work/file_share)
[NFS protocol, 디렉토리 공유하기](/Area/Programming/Remote_Work/NFS_protocol,_디렉토리_공유하기)
[SSH protocol](/Area/Programming/Remote_Work/SSH_protocol)
[rclone으로 클라우드 스토리지 서비스 동기화](/Area/Programming/Remote_Work/rclone으로_클라우드_스토리지_서비스_동기화)

#### Operating System

[HUB: Operating System](/Area/Programming//Operating_System/index.md)

1. 설치&복제
2. 서비스 관리
3. system Packages & Apps
4. Shell & Terminal & Editor
5. secretes 관리
6. etc

#### Skills

[HUB: Skills](/Area/Programming/Skills/index.md)

- Container
- Web Dev
- Chrome Extension
- Cloud Computing
- CI CD
- Languages
- Web Scraping
- Big Data
- Database
- Network

#### Experience

##### Architecture

내가 그동안 설계한 것들 정리해두면 좋겠다.


##### Debugging

- 미결
  [docker named volume with NFS protocol](/Area/Programming/Experience/Debugging/docker_named_volume_with_NFS_protocol)


- 해결
  [domain status: clientHold](/Area/Programming/Experience/Debugging/domain_status:_clientHold)
  [프로세스 조회 및 종료](/Area/Programming/Experience/Debugging/프로세스_조회_및_종료)

##### etc

[vim을 왜 쓸까?](/Area/Programming/Experience/etc/vim을_왜_쓸까?)
[gitsigns.nvim plugin에 이슈 남기기](/Area/Programming/Experience/etc/gitsigns.nvim_plugin에_이슈_남기기)
[nvim 플러그인에 이슈 남기기](/Area/Programming/Experience/etc/nvim_플러그인에_이슈_남기기)
[오픈소스에 난생처음 PR 날리기](/Area/Programming/Experience/etc/오픈소스에_난생처음_PR_날리기)
[Arch Linux 패키지 업데이트 후 X11 문제 해결](/Area/Programming/Experience/etc/Arch_Linux_패키지_업데이트_후_X11_문제_해결)


#### AI


> [!lg] Log 2025-05-18
>  
> 통상 2가지 작업을 한다.
>
>   - 설득/보고를 위한 탐색 => research
>   - 소프트웨어 결과물 => code
>
> 각각의 목적에 맞는 파이프라인이 있으면 좋다.


> [!rf]
>
> [“AI는 없는 인간만의 뭐가 있다? 그런 말 하는 사람 다 밀려났다.” (장강명 작가)](https://www.youtube.com/watch?v=bBaTc1JE42w)
> [대기업 AI 개발자 되는 방법부터 하는 일까지! 3명의 현직자가 다 알려드립니다ㅣGPU 최적화, LangChain, RAG, Kubernetes](https://www.youtube.com/watch?v=MWsOZ2IhtiU&pp=0gcJCY0JAYcqIYzv)


##### research

- 탐색 및 심층 이해

  1. 프롬프트 생성: [maskara.ai](https://www.maskara.ai/)
  2. 기초조사 및 출처 얻기: [perplexity.ai](https://www.perplexity.ai/)
  3. 출처 정보를 탐색: [notebooklm.google.com](https://notebooklm.google.com/)


- 데이터에서 인사이트 도출: [storytell.ai](https://storytell.ai/)


- 데이터베이스 설계: [database.build](https://database.build/)

  - 어떤 논리를 위해 어떤 데이터가 수집되어야 하는가? 
  - 어떤 서비스를 진행하는데 통상 어떤 데이터를 수집하는가?


##### code

- Current
  [aider](/Area/Programming/AI/code/aider)
  - github copilot with ChatCopilot.nvim
  - chatGPT web UI
  - codecompanion.nvim


- Interesting
  - devin(commercial) & [devica(open-source)](https://github.com/stitionai/devika/discussions)
    : agent-like

- Abandoned
  - avante.nvim

##### etc

[개념정리: what is chatGPT?](/Area/Programming/AI/etc/what_is_chatGPT?)
[블로그 읽기: You are using Cursor AI incorrectly... 09 Feb 2025](/Area/Programming/AI/etc/블로그_읽기:_You_are_using_Cursor_AI_incorrectly..._09_Feb_2025)
[유투브 시청: AI로 아마존 코딩 테스트 뚫고, 36일 만에 13억 매출을 만든 21살 | Cluely, Chungin (Roy) Lee](/Area/Programming/AI/etc/유투브_시청:_AI로_아마존_코딩_테스트_뚫고,_36일_만에_13억_매출을_만든_21살_|_Cluely,_Chungin_(Roy)_Lee)

#### 알고리즘

[알고리즘 게임: CodinGame](https://www.codingame.com/training)

- 가이드라인 (discord에서 주워들었다.)

  ```txt
  1. 근데 알고리즘도 뭐 그렇게 어렵지는 않고, 아래에서 크게 벗어나지는 않더라구요.
     - 문법 숙지 -> 브루트포스 -> 백트래킹 -> 자료구조 공부(STL) -> DFS -> BFS -> 다익스트라 ->
       유니온파인드 -> MST (이후)
     - 정렬
     - 이분탐색
     - 그리디
     - 기본적인 DP
     - 비트마스킹
     - 유클리드호제법
     - 에라토스테네스의 체

  2. 저희 회사도 막 신박한 희안한 알고리즘 가져와서 풀어야되는걸 내기보다는 알고리즘을 제대로
     이해했는지, 제약사항이 주어졌을 때 어떻게 개선할 수 있고 얼마나 개선됐는지 설명할 수 있는지 이런걸
     봐요. Big O 노테이션으로다가.

  3. 리트코드가 신빙성 있고, medium 정도면 차고도 넘침
  ```


### 좋은 가족, 좋은 친구, 멋진 인간 하기

[선물 하고싶은 것](/Area/좋은_가족,_좋은_친구,_멋진_인간_하기/선물_하고싶은_것)
[감사한 마음](/Area/좋은_가족,_좋은_친구,_멋진_인간_하기/감사한_마음)
[다른 입장을 이해해 보기](/Area/좋은_가족,_좋은_친구,_멋진_인간_하기/다른_입장을_이해해_보기)

[매형과의 대화](/Area/좋은_가족,_좋은_친구,_멋진_인간_하기/매형과의_대화)



### 글쓰기

> [!rf]
>
> [글쓰기 레퍼런스 모음](/Area/글쓰기/뭐든지/글쓰기_레퍼런스_모음)


#### 뭐든지

[대학교를 중퇴한 이유가 뭔가요?](/Area/글쓰기/뭐든지/대학교를_중퇴한_이유가_뭔가요?)
[10시 이후로는 언어정보의 input을 제한한다.](/Area/글쓰기/뭐든지/10시_이후로는_언어정보의_input을_제한한다.)
[우리는 상대방을 눈앞에 두고도 그 실체를 보지 못한다. 단지, 내 방식대로 이해한 허상을 바라볼 뿐이다.](/Area/글쓰기/뭐든지/우리는_상대방을_눈앞에_두고도_그_실체를_보지_못한다._단지,_내_방식대로_이해한_허상을_바라볼_뿐이다.)
[살아오면서 겪어본 어려운 문제에 대하여](/Area/글쓰기/뭐든지/살아오면서_겪어본_어려운_문제에_대하여)


#### 유투브 시청

['아들 학폭' 복수 나선 아빠, 판사도 "그럴 만했네.." 무죄 (2024.12.21/MBC뉴스)](/Area/글쓰기/유투브_시청/'아들_학폭'_복수_나선_아빠,_판사도_"그럴_만했네.."_무죄)
[연금개혁으로 꿀빨았다는 50대, 과연 안 억울할까? (변명)](/Area/글쓰기/유투브_시청/연금개혁으로_꿀빨았다는_50대,_과연_안_억울할까?_(변명))
[유튜브가 당신의 지적 성장을 가로막는 이유](/Area/글쓰기/유투브_시청/유튜브가_당신의_지적_성장을_가로막는_이유)


> [!rf]
>
> [사랑하는 방법(팔란티어 접근 방식)](https://www.youtube.com/watch?v=zyXgqQkWULU)
>   - 이 사람은 구독하고 잘 봐야겠다.
>   - 심지어 투자를 하고싶은 마음까지 들게 만든다.
> 
> [조건을 안보기 시작한 IT 업계](https://www.youtube.com/watch?v=M8oj2ZIoYbY)


### 나를 사랑하기


#### 운동

- 부분 운동
  1/ 2, [팔 이두, 10m](https://www.youtube.com/watch?v=BfZw0qMoa1A)
  0/ 2, [팔 삼두, 10m](https://www.youtube.com/watch?v=DqnWLrrO0xY)
  0/ 2, [등, 10m](https://www.youtube.com/watch?v=BGd4sKOcl5I)
  1/ 2, [어깨, 10m](https://www.youtube.com/watch?v=Lts-ddUgSFQ)

- 전신 운동
  0/ 0, [전신, 12m](https://www.youtube.com/watch?v=vl4S_e_dBuk)
  0/ 1, [전신, 10m](https://www.youtube.com/watch?v=8wEdqDDzHpc)
  0/ 1, [전신, 8m](https://www.youtube.com/watch?v=C80QyNlAMss)
  0/ 1, [전신, 7m](https://www.youtube.com/watch?v=g-nzEPqqrx8)
  0/ 1, [전신, 6m](https://www.youtube.com/watch?v=LDb5Y4Ti6Bc)
  0/ 1, [전신, 5m](https://www.youtube.com/watch?v=XeWwQK-I2Tg)

#### 영어

[오답노트](/Area/나를_사랑하기/English_오답노트)

> [!rf]
>
> [이렇게 공부했더니 통역사가 되었습니다.](https://www.youtube.com/watch?v=A3X-n6bwVa0)



[이 잡지로 영어 공부하면… 생각이 달라져요](https://www.youtube.com/watch?=hea9__pHHGQ) 
[the economist](https://www.youtube.com/watch?=hea9__pHHGQ)


### 민주 시민의 책임 & 권한 & 상식

[세금 납부](/Area/민주_시민의_책임_&_권한_&_상식/세금_납부)
[각종 서류](/Area/민주_시민의_책임_&_권한_&_상식/각종_서류)


## Resource


### Design

- 피그마 스타일 가이드
  [소개](https://www.youtube.com/watch?v=fFNeuHAL3-E)
  [스타일 가이드 소개](https://www.youtube.com/watch?v=_7wO8CZEwBw)


- 학습
  [css 학습 채널](https://www.youtube.com/@lundeveloper)
  [youtube: how to design with figma? (A)](https://www.youtube.com/watch?v=6YpAl-U1ASU)
  [youtube: how to design with figma? (B)](https://www.youtube.com/watch?v=h1gtRXskgoY)
  [youtube: how to design with figma? (C)](https://www.youtube.com/watch?v=l3A9OcUd_Us)


### 유용한 무료 api

- 영영사전
  : 발음, 예문, 동의어, 반의어 등 다양한 정보를 제공한다.
  ['darkness' 사전 검색 요청](https://api.dictionaryapi.dev/api/v2/entries/en/darkness)


### 지능

지능은 다양한 요소로 구성된다.

- 정서
  - 자기 불안을 다스리는 능력

- 사회성
  - 사고의 유연성
  - 추론 능력
  - 문제 해결력



### 학습법

- 무언가를 배우는 방법

  > 1. 공부 하려는 주제에 대해 조사한다.
  > 2. 설명을 적어 본다. 가상의 10살 어린이를 앞에 두고.
  > 3. 잘 모르는 부분, 지나치게 복잡한 부분(전문용어를 사용했다던가)을 찾는다.
  > 4. `3.`에서 찾은 내용을 더 쉽고 단순하게 설명하기 위해 추가적으로 조사한다.


### 대중문화

[보고싶은 영화 리스트](/Resource/대중문화/보고싶은_영화)
[인물](/Resource/대중문화/인물)
[음악](/Resource/대중문화/음악)



### 여행

[가고싶은 식당](/Resource/여행/가고싶은_식당)



### 차량 관리

- 청주시 서원구 기후대기과:
  - 043)201-4985
  - 2024/04/29 17:30 쯤 전화통화

- 청주시 차량등록사업소 세무팀
  - 043)201-4951
  - 2024/04/29 15:37 전화통화

- 올드카 성지: 이화카센터
- 올드카 성지: 서광캬브레타


[비용 기록](/Resource/차량_관리/비용_기록)
[베터리 자가교체](/Resource/차량_관리/베터리_자가교체)
[정기검사지연 과태료와 운행정지명령](/Resource/차량_관리/정기검사지연_과태료와_운행정지명령)
[청주시의 미세먼지 비상저감조치](/Resource/차량_관리/청주시의_미세먼지_비상저감조치)




### etc

[주방기구: 스사모 통즈 구입](/Resource/etc/주방기구:_스사모_통즈_구입)

[pdf 문서 편집](/Resource/etc/pdf_문서_편집)

['언어는 사용되어야 한다.' 의미가 점점 사라지는 이유](/Resource/etc/언어는_사용되어야_한다.)


> [!rf]
>
>
> [건전지 사용 제품을 충전지 방식으로 개조하기](https://www.youtube.com/watch?v=-zltotyU1Ek)
> [커뮤니티 구축 방법론](https://www.jianyang.co.kr/p/7-feat?utm_source=publication-search)
> [better way to manage dot files](https://www.youtube.com/watch?v=tBoLDpTWVOM)
> [self hosted secrets manager](https://www.youtube.com/watch?v=7t5M4FXqs9E&list=WL&index=29)
> [e-commerce example site](https://contents.clayful.store/)
> [good programmer](https://parksb.github.io/article/32.html)
> 
> > [강력추천! 영상 크리에이터라면 꼭 알아야 할 7가지 웹사이트]([https://www.youtube.com/watch?v=yYwJI9y_arE)
> >
> > === Timestamps ===
> > 00:00 인트로
> > 00:23 레퍼런스 사이트1
> > 01:12 레퍼런스 사이트2
> > 02:02 레퍼런스 사이트3
> > 02:22 레퍼런스 사이트4
> > 02:49 레퍼런스 사이트5
> > 03:22 레퍼런스 사이트6
> > 03:56 레퍼런스 사이트7
> > 03:56 아웃트로
> >
> > ▶레퍼런스 사이트 링크들 입니다.
> > https://eyecannndy.com/
> > https://kive.ai/discover
> > https://film-grab.com/
> > https://shot.cafe/
> > https://www.cosmos.so/
> > nytimes.com/column/anatomy-of-a-scene




## Archive

- Project
  [칭찬 수집기 개발](/Archive/Project/칭찬_수집기)
  [2025 우테코 지원](/Archive/Project/2025_우테코_지원)
  [우정한사랑 이사](/Archive/Project/우정한사랑_이사)
  [gatsby로 블로그](/Archive/Project/gatsby로_블로그/index.md)
  [경주 골든 페스티벌](/Archive/Project/경주_골든_페스티벌)
  [성금 엄마아빠집 리모델링](/Archive/Project/성금_엄마아빠집_리모델링/index.md)
  [CBPM 견적서 관리 웹앱 개발](/Archive/Project/CBPM_견적서_관리_웹앱_개발/index.md)




## How To Note: PARA


**PARA 메모법**

> - Project:
>   - **지금 가장 전념**하는 것
>   - 기간/목표가 명확함. 즉, 끝이 있음
>   - ex) 이사하기, 직장 취업하기, 책 쓰기
>  
> - Area:
>   - **끝이 없고, 꾸준히** 해 나가는 것
>   - 나의 책임이나 역할이 있음
>   - ex) 운동, 독서, 프로그래밍 실력 향상
>
> - Resource:
>   - 관심 분야 정보
>   - 내 **책임은 없음**
>   - ex) 창업, 종교, 예술, 춤추는법 등
>
> - Archive:
>   - 위의 세 가지에 속하지 않는 것
>   - 종료/중단된 프로젝트
>   - 책임이 사라진 영역, 관심이 사라진 자원
