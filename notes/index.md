#  DEV WIKI



_이 위키의 목적은 기록하여 (나중에)필요시 되찾아보기 위함이다.
누군가에게 뽐내기 위한 용도가 아니다._

_"더 열심히 해"_ from.고은상


## Project

### 프로그래밍

> [!re] 일시 중단
>
> [견적서 관리](/Project/진행중/견적서_관리/index.md)
> - 최초 설계가 잘못되었는데, 설계 부터 다시 하기엔 당장 시급한 일이 있다.


### 결혼에 확신을 갖는 방법 찾기

호호호


## Area

### 오픈소스 기여 [-]


#### neovim 내장 플러그인 tohtml.vim [-]

- 발단

  `render-markdown.nvim`으로 렌더링(하이라이팅) 된 것을 있는 그대로 pdf 또는 이미지로 출력하고싶었다. `TOhtml` 명령을 이용해보란 조언을 얻었다.(vim.kr 디스코드 채널)

  그런데 vimwiki에서 당장 TOhtml 해보면 에러가 발생하는거다. 이것에 대해 대화하다가 아래와같은 조언을 얻었다.

  ```txt
  tohtml이 루아로 변환된지 오래되지 않아서 어쩌면 네오빔 업스트림에 기여하실수도있어요
  버그 고치신다면 리뷰하고 받아주실거같아요
  html 나온거에서 </pre> 에 해당하는 부분
  그 부분 계산하다가 태그가 안맞아서 뻗은거네요
  <pre> .. </pre> 사이에 태그가 뭔가 빠졌다는건데
  ```

  [tohtml.vim](https://github.com/neovim/neovim/blob/3d3a99e69cda365cae9ad65831712301807a772b/runtime/lua/tohtml.lua)


### 생산성을 기르자

> [!rf]
>
> [The LinkedIn DPH Framework](https://linkedin.github.io/dph-framework/)
> [지식근로자의 생산성](https://www.joinc.co.kr/w/knowledge-workder-productivity)


> [!ye] 나의 성과를 측정/평가 하자
>
> - 생산적인 **기간**을 보내기 위해서는...
>
>   1. **나를 평가하는 사람**의 기준을 명확하게 인지해야 한다.
>   2. **나 스스로**의 기준이 명확하게 있어야한다.(즉, 나의 목표 부터가 명확해야 한다.)
>
> - 생산적인 **하루**를 보내기 위해서는...
>
>   1. 큰 일을 작은 일로 나눈다.
>   2. 완료의 기준을 명확히 한다.
>   3. 코딩도 마찬가지다.


#### Note Making

> [!rf]
>
> [From note-taking to note-making](https://nesslabs.com/from-note-taking-to-note-making)


[ Document](/Programing/tools/documentation/index.md) [-]


> [!ye] PARA 메모법
>
>  _실행도를 기준으로 노트를 분류하는 기법. 주제별로 나누는 것이 아니다._
>
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


#### Post

[개발자로서 어떻게 살아남을까?](개발자로서_어떻게_살아남을까?)

>[lg] 2024-09-09
>
>
>  > [!rf] 아래 영상들을 보고서...
>  >
>  > [개발자는 어떻게 살아남을까](https://www.youtube.com/watch?v=U55ex_ga7_k)
>  > [개발자는 어떻게 살아남을까](https://www.youtube.com/watch?v=M2T-4SoLY-0)
>
>
>- 쉽게 변하지 않는 본질적인 것들에 집중하자.
>
>  ex) 과거 Hadoop/Hbase같은걸 세팅하려면 몇달이 걸렸다. 지금은 즉시 사용할 수 있는 SaaS가 있다.
>  ex) garbage collecter의 등장. 직접 메모리 관리가 필요한 경우가 줄었다.
>
>
>- 유망한 영역
>
>  - 메타버스: 하드웨어와 소프트웨어가 함께 발전 중
>  - 블록체인: 메타버스 환경에서의 NFT나 화폐기술
>  - AI, Bigdata: 업무의 페러다임을 바꿈




### 개발 환경

#### dotfiles [-]

_dotfiles만 제대로 관리하면 하드웨어가 바뀌어도 빠르게 개발/생산 환경을 구축할 수 있다._

> [!td]
> 아래 살펴보고 나도 제대로 관리해보자.
>
> [better way to manage dot files](https://www.youtube.com/watch?v=tBoLDpTWVOM)
> [self hosted secrets manager](https://www.youtube.com/watch?v=7t5M4FXqs9E&list=WL&index=29)
> [secure auth for everything](https://www.youtube.com/watch?v=N5unsATNpJk)

#### Command History

[command history](/Cheat_Sheet/command_history)

#### Remote System

[file share & control](/Cheat_Sheet/file_share_&_control)
[nfs 프로토콜, 디렉토리 공유하기](/Programing/workflows/서버관리/nfs로_디렉토리_공유하기)
[SSH protocol](/Programing/workflows/서버관리/SSH)
[rclone으로 클라우드 스토리지 서비스 동기화](/Programing/workflows/cloud_storage/rclone으로_클라우드_스토리지_서비스_동기화)

#### Operating System

[만자로 시스템 복사(이사)하기](/Programing/concepts_and_tools/concepts/linux/만자로_시스템_복사하기)
[sysz](/Programing/tools/system_admin/sysz.md): `systemctl`을 편리하게!
[System configs](/Cheat_Sheet/System_configs)
[부팅디스크 만들기](/Area/개발_환경/Operating_System/부팅디스크_만들기)

> [!ye] 리눅스 종합 system configurations 관리 툴(아직 한창 개발중):
>
> [제작자 소개](https://www.youtube.com/watch?v=Zt0HnIMbzZQ)
> [github](https://github.com/ChrisTitusTech/linutil)


> [!ye] x11 세션에서 wayland 세션을 실행할 수 있다.(session in session)
>
> weston DE를 활용해서 가능한데,
>
> ```bash
> # weston 설치
> sudo pacman -S weston
>
> # background 실행
> weston --backend=x11-backend.so
> ```

#### Vim

[Vim](/Programing/tools/vim/index.md)

#### Git & Github

[Git](/Programing/tools/git_and_github/index.md)

#### Browser

[browser](/Programing/tools/browser/index.md)
  > rave browser

#### AI

[AI](/Programing/tools/AI/index.md)
  > aider

#### Tools

[bore](/Programing/concepts_and_tools/tools/bore.md):
  simple tcp tunnel CLI. make localhost to public server


#### Post

[vim을 왜 쓸까?](/Programing/posts/vim을_왜_쓸까?)
[gitsigns.nvim plugin에 이슈 남기기](/Programing/posts/gitsigns.nvim_plugin에_이슈_남기기)
[nvim 플러그인에 이슈 남기기](/Programing/posts/nvim_플러그인에_이슈_남기기)
[오픈소스에 난생처음 PR 날리기](/Programing/posts/오픈소스에_난생처음_PR_날리기)


### 개발 역량 [-]

_프로덕션에 가까운 프로젝트들을 보면서 배우는 것이 중요하다._
_"Fediverse가 프로덕션에 가깝게 되어있는게 많아요. 루비는 마스토돈, nodejs는 미스키, 파이썬은 bookwyrm"_

#### Linux

[linux `$HOME` path](/Programing/concepts_and_tools/concepts/linux/home_path)
[systemd](/Programing/concepts_and_tools/concepts/linux/systemd)
[what is shell?](/Area/개발_역량/Linux/what_is_shell)

#### Container

[docker](/Programing/concepts_and_tools/tools/docker.md):
[HUB: kubernetes](/Programing/concepts_and_tools/tools/kubernetes/index.md):

#### Web Dev

[static website vs dynamic website](/Programing/concepts_and_tools/concepts/web_dev/static_website_vs_dynamic_website)
[architecture](/Area/개발_역량/Web_Dev/architecture)
[design](/Area/개발_역량/Web_Dev/design)

#### Cloud Computing

[AWS](/Area/개발_역량/Cloud_Computing/AWS)


#### CI CD

[!ye] 배포 자동화는 고객의 요구사항을 빠르게 반영하는 핵심 요소이다. 요구사항을 파악하고 빠르게 반영하기도 바쁜데, 배포가 매끄럽지 못해서 턱턱 막힌다면 진짜 갑갑하다.

> [!ye] single container SPA
>
> [실습 github](https://github.com/study-and-trial/static-site)
>
> - 파이프라인 구성요소
>   - dev 서버
>   - github-action runner 서버
>   - container (image)registry
>   - 배포 서버
>
> - 작업 순서
>   1. 개발 진행
>   2. github push 또는 pr merge에 따라 runner 실행(적절한 방식을 runner의 트리거로 설정한다.)
>   3. runner server(github 또는 self-hosted)에서 runner 실행:
>      1. container (image)registry 로그인
>      2. 컨테이너 이미지 빌드, 레지스트리에 push
>      3. 배포서버에 ssh 접속하여 이미지 pull, 컨테이너 재시작

#### Languages

[Javascript](/Area/개발_역량/Languages/Javascript) [-]
[Dart](/Area/개발_역량/Languages/Dart) [-]
[Python](/Area/개발_역량/Languages/Python)

#### Web Scraping

> [!lg]웹 접근성 기반 선택자를 활용하자
>
> - 접근성 기반 선택자 사용의 실제 예시
>
>   예를 들어, 다음과 같은 HTML 코드가 있을 때:
>
>   html
>   코드 복사
>   <button aria-label="Download">Download</button>
>   이 버튼을 선택하려면 다음과 같은 선택자를 사용할 수 있습니다:
>
>   javascript
>   코드 복사
>   document.querySelector('button[aria-label="Download"]');
>   이 선택자는 aria-label 속성에 "Download"가 설정된 모든 버튼을 선택합니다. 버튼의 클래스나 ID가 변경되더라도 이 선택자는 유지될 가능성이 큽니다.
>
>   접근성 기반 선택자는 웹 스크래핑, 자동화 테스트, UI 테스트 등에서 안정적으로 사용할 수 있는 강력한 도구입니다.

#### Big Data

[Keywords](/Area/개발_학습/Big_Data/Keywords)
[Apache arrow](/Area/개발_학습/Big_Data/Apache_arrow)
[Big Data Engineering](/Programing/workflows/big_data_engineering/index.md)

#### Database

[관계형 데이터베스의 종류](/Programing/concepts_and_tools/concepts/database/relational_database/relational_database의_종류.md)
[기본개념 및 키워드](/Programing/concepts_and_tools/concepts/database/relational_database/기본개념_및_키워드.md)
[SQL 기본개념, Attribute Type, Key Constraints](/Programing/concepts_and_tools/concepts/database/relational_database/SQL_기본개념,_Attribute_Type,_Key_Constraints.md)
[SQL query](/Programing/concepts_and_tools/concepts/database/relational_database/SQL_query.md)
[SQL에서 NULL의 의미](/Programing/concepts_and_tools/concepts/database/relational_database/SQL에서_NULL의_의미.md)
[SQL과 JOIN](/Programing/concepts_and_tools/concepts/database/relational_database/SQL과_JOIN.md)
[SQL과 정렬 및 통계](/Programing/concepts_and_tools/concepts/database/relational_database/SQL과_정렬_및_통계.md)
[데이터베이스 트랜잭션, ACID](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_트랜잭션,_ACID.md)
[데이터베이스 설계-정규화](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_설계-정규화.md)
[데이터베이스 설계-DB index](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_설계-DB_index.md)
[데이터베이스 설계-파티셔닝, 샤딩, 레플리케이션](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_설계-파티셔닝,_샤딩,_레플리케이션.md)
[데이터베이스 성능 최적화-서버 리소스 활용](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_성능_최적화-서버_리소스_활용.md)
[데이터베이스 성능 최적화-DB connection pool](/Programing/concepts_and_tools/concepts/database/relational_database/데이터베이스_성능_최적화-DB_connection_pool.md)
[RDB vs NoSQL](/Programing/concepts_and_tools/concepts/database/relational_database/RDB_vs_NoSQL.md)

> [!rf]
> [강의 재생목록](https://www.youtube.com/playlist?list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe)


[-] [SQL에서 stored Function 및 stored Procedure](/Programing/concepts_and_tools/draft/SQL에서_stored_Function_및_stored_Procedure)
[-] [draft/SQL concurrency control, 트랜잭션의 Isolation 보장하기](/Programing/concepts_and_tools/draft/SQL_concurrency_control,_트랜잭션의_Isolation_보장하기)


#### 문제 해결: 미결

[docker named volume with NFS protocol](/Programing/problem_solving/docker_named_volume_through_NFS_protocol)

#### 문제 해결: 해결

[domain status: clientHold](/Programing/problem_solving/domain_status:_clientHold)
[프로세스 조회 및 종료](/Programing/problem_solving/프로세스_조회_및_종료)

#### Post

[I just wanted to deploy my code!](/Programing/posts/I_just_wanted_to_deploy_my_code!)
[what is chatGPT](/Programing/posts/what_is_chatGPT)


#### Reference

- etc
  [부트캠프 팀 과제 커밋 히스토리 보는게 도움될듯](https://github.com/boostcampwm-2022)
  [7 Habits For Effective Text Editing](https://www.youtube.com/watch?v=eX9m3g5J-XA)
  [bash-shell, sed, awk, make, macro: 한글 가이드 문서](https://mug896.github.io/bash-shell/bug_reports.html)

- backend course
  [web-course](https://www.boot.dev/tracks/backend)
  [github](https://github.com/bootdotdev/curriculum)

- 시간 죽이는 개발자 게임
  [ 알고리즘 게임: CodinGame](https://www.codingame.com/training)
  [CSS 게임: Css Diner](https://flukeout.github.io/)


- 알고리즘

  1. 근데 알고리즘도 뭐 그렇게 어렵지는 않고, 아래에서 크게 벗어나지는 않더라구요.
    - 문법 숙지 -> 브루트포스 -> 백트래킹 -> 자료구조 공부(STL) -> DFS -> BFS -> 다익스트라 -> 유니온파인드 -> MST
     (이후)
    - 정렬
    - 이분탐색
    - 그리디
    - 기본적인 DP
    - 비트마스킹
    - 유클리드호제법
    - 에라토스테네스의 체

  2. 저희 회사도 막 신박한 희안한 알고리즘 가져와서 풀어야되는걸 내기보다는 알고리즘을 제대로 이해했는지, 제약사항이 주어졌을 때 어떻게 개선할 수 있고 얼마나 개선됐는지 설명할 수 있는지 이런걸 봐요. Big O 노테이션으로다가.
  3. 리트코드가 신빙성 있고, medium 정도면 차고도 넘침

- 따라하기
  [chrome todo extension in JS](https://www.youtube.com/watch?v=ny-L_KLrKIU)

- 개발 일반
  [The twelve-factor app](https://12factor.net/)
  [Harvard CS 50 (2023) – Full Computer Science University Course](https://www.youtube.com/watch?v=LfaMVlDaQ24)
  [파이썬 버전이 달라지며 뭐가 바뀌었나?](https://www.youtube.com/watch?v=XnfsucgWApE)
  [천재들이 만든 웹의 구조는 실제로 이렇게 동작합니다.](https://www.youtube.com/watch?v=po5_T3wSPJI)

- 잘 정리된 튜토리얼들, `TS`, `python` 등 간단한 주제별로 있다.
  [tutorials: English](https://www.squash.io/tutorials/)
  [tutorials: Korean](https://www.joinc.co.kr/w/architecture)
  [30-Days-Of-Python](https://github.com/Asabeneh/30-Days-Of-Python)

- big data
  [free-ebook: "Data Wrangling with JavaScript"](https://www.manning.com/books/data-wrangling-with-javascript?a_aid=datawranglingwithjavascript&a_bid=acc654f9)
  [**유료강의:** 빅데이터 파이프라인 마스터; 성공을 위한 도구와 기술](https://www.inflearn.com/course/%EB%B9%85%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-%EB%A7%88%EC%8A%A4%ED%84%B0?inst=dcf981c7&utm_source=instructor&utm_medium=referral&utm_campaign=inflearn_%ED%8A%B8%EB%9E%98%ED%94%BD_promotion-link)
  [빅데이터 플랫폼 특론: play-list](https://www.youtube.com/playlist?list=PLCsebpDZm0n6HYSDaNxKQYrNrD4Xk9meX)
  [하둡 분산파일 시스템 구축: play-list](https://www.youtube.com/watch?v=g6xIMSYjh0w&list=PLY-_9hx4ldZwYOjtfRT0MV2k9JcnTUYW2)

- docker, k8s
  [docker, k8s, microservcie](https://www.youtube.com/watch?v=xdqOxF2JqwU)
  [best k8s tool](https://www.youtube.com/watch?v=R0HlJsugOAE)
  [develop in kub, not in docker-compose](https://www.youtube.com/watch?v=RTo9Pvo_yiY)
  [docker network extra usuage](https://www.youtube.com/watch?v=bKFMS5C4CG0)
  [kub network](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-networking-guide-beginners.html)

- 게임 개발
  [초보자를 위한 게임개발 총론](https://gpgstudy.com/gpgiki/%EC%B4%88%EB%B3%B4%EC%9E%90_Faq)
  [flutter game dev Big series](https://www.youtube.com/watch?v=Kwn1eHZP3C4&ab_channel=Spellthorn)
  [flutter game dev Small series](https://www.youtube.com/watch?v=wUf3UytV4wQ&ab_channel=TreyCodes)


### 구독 서비스 관리

#### 개발

- subscribe
  - github copilot: $100/Y
  - openAI: gpt: $20/M
  - figma: $15/M

- API
  - anthropic: claude.ai

- AWS


### 좋은 가족, 좋은 친구 되기

#### 선물 하고싶은 것들

[좋은 가족, 좋은 친구 되기](/Life/좋은_가족,_좋은_친구_되기/index.md)



### 글쓰기

- 유투브(노트 생성)
  > [!ye] 대단해
  >
  > - 이 사람은 구독하고 잘 봐야겠다.
  > - 심지어 투자도 하고싶다.
  > [사랑하는 방법(팔란티어 접근 방식)](https://www.youtube.com/watch?v=zyXgqQkWULU)


[수필](/Life/수필/index.md)
  > [!ye] 우리는 상대방을 눈앞에 두고도 그 실체를 보지 못한다. 단지, 내 방식대로 이해한 허상을 바라볼 뿐이다.
  >
  > 자주 이 사실을 잊고, _"너 나를 그렇게 생각한거야?"_라고 묻게 된다.
  >
  > 그래서 상대가 나를, 내가 상대를 오해하고 있지 않은지 자주 확인해볼 필요가 있다.
  >
  > _"너 (이 부분에 대해) 내가 어떻게 생각하고 있다고 생각해?"_
  >
  > 또한 내가 누군가를, 누군가 나를 평가 또는 판단 할 때 그것이 상대의 실체가 아닌 이해의 허상이라는 것을 유념하자.

  [살아오면서 겪어본 어려운 문제에 대하여](살아오면서_겪어본_어려운_문제에_대하여)
  >내가 겪은 가장 어려운 문제는, '어떻게 살아야 하는가'라는 질문에 답을 찾는 일이었다.
  >
  >최근 우테코에 지원하려고 시도했는데, 지원서의 작성 항목에 '겪어본 어려운 문제'가 있었다. 흔하다면 흔한 질문인데, 금방 대답이 나오지 않았다. 내가 쉬운 선택만 해왔나? 아니면 능력이 좋아서 매사 잘 해내고 이겨냈나? 그렇지는 않았다.
  >
  >오히려 중구난방으로 살다보니 매번 새로운 난관에 부딪혔고, 기대한 만큼의 성과를 얻는게 드물었다. 선택과 집중을 못했을 수도 있고, 때때로 결단력이 부족했을 수도 있다. 관심 가진 것들에 실력도, 재능도 부족했을지 모른다. 노력이 부족했을 수도 있다. 꾸준함도 부족했을 수도 있다.
  >
  >그래도 나는 좌절하지 않았다.
  >
  >내가 워낙 긍정적이라서? 그런 측면도 분명 있다. 최악의 경우를 떠올려보고도 감당 가능하다면, 나는 곧잘 태평해진다. 하지만 그보다 더 큰 이유는, 내가 사실은 그 어느것에서도 실패했다고 여기지 않기 때문이다.
  >
  >나는 20살에 벌써 얕은 판단력으로 대학을 내팽개쳤다. 니체가 말하는 '낙타'처럼 살아왔는데, 갑자기 '사자'처럼 살기 시작했다. '원래 세상이 그렇다'같은 모든 종류의 신호를 의심하기 시작했고, 의견을 동냥하든 찍어먹어보든 스스로 판단을 내리기 시작했다. 더 나은 미래에 대한 희망을 지켜내는 것 부터, 삶의 의미를 찾아내는 것까지 20대 중반이 되어서야 마쳤다. 그제서야 군대도 갔다.
  >
  >사정이 이렇다 보니 사회인으로서 본격적으로 시작한 것은 비교적 늦은 것 같다. 현실 세계에서, 사람들과 사회에 기여하는 능력이란건 튼튼하고 건강한 마음과 완전히 일치하지는 않기 때문에 고전을 면치 못하고 있다. 그러나 왕도는 없다. 최선을 다해서 똑똑하게, 열심히 일하고 공부하는 것 뿐이다.
  >
  >
  >그래서 내가 겪은 가장 어려운 문제는, '어떻게 살아야 하는가'라는 질문에 답을 찾는 일이었다. 스무살의 나에게는 그 때 그것이 가장 중요한 문제였고, 그 결과로 지금 갖가지 어려움 속에서도 흔들림 없이 살아가고 있다.
  >
  >
  >_이건 너무 짧고 생략된 버전이다. 과정에 구체적으로 어떤 일들이 있었는지, 당시 가장 고민했던 점은 무엇인지 개괄적으로 작성해주면 좋겠다._


- 레퍼런스 모음(노트 생성)
  - 레퍼런스에도 반드시 내 생각을 덧붙이자.

  > [!ye] 허준이 교수, 졸업식 축사
  >
  > 이제 본격적으로 어른입니다. 실패를 두려워하지 말고 도전하라. 편안하고 안전한 길을 거부하라. 타협하지 말고 자신의 진짜 꿈을 쫓아라. 모두 좋은 조언이고 사회의 입장에서는 특히나 유용한 말입니다만, 개인의 입장은 다를 수 있음을 여러분은 이미 고민해 봤습니다. 제로섬 상대평가의 몇 가지 통명스러운 기준을 따른다면, 일부만이 예외적으로 성공할 것입니다.
  >
  > 여러 변덕스러운 우연이, 지쳐버린 타인이, 그리고 누구보다 자신이 자신에게 모질게 굴 수 있으니 마음 단단히 먹기 바랍니다. 나는 커서 어떻게 살까, 오래된 질문을 오늘부터의 매일이 대답해줍니다.
  >
  > 취업 준비, 결혼 준비, 육아 교육 승진 은퇴 노후 준비를 거쳐 어디 병원 그럴듯한 일인실에서 사망하기 위한 준비에 산만해지지 않기를 바랍니다. 무례와 혐오와 경쟁과 분열과 비교와 나태와 허무의 달콤함에 길들지 말길, 의미와 무의미의 온갖 폭력을 이겨내고 하루하루를 온전히 경험하길, 그 끝에서 오래 기다리고 있는 낯선 나를 아무 아쉬움 없이 맞이하길 바랍니다.


- 어휘력(노트 생성)

- 표현력(노트 생성)


> [!rf]
>
> [온라인 글쓰기 1편: 좋은 **질문**하기](https://github.com/WieeRd/questionable/blob/master/i18n/ko.md#%EA%B0%84%EB%8B%A8-%EC%9A%94%EC%95%BD)
> [온라인 글쓰기 2편: 좋은 **답변**하기](https://edykim.com/ko/post/tech-has-a-toxic-tone-problem-lets-fix-it/)



### 운동

[운동](/Life/운동/index.md)

### 차량 관리

[Car](/Life/car/index.md)

### 민주 시민의 책임

[민주 시민의 책임과 권한](/Life/democratic_citizen/index.md)


## Resource

### Design

#### Tools

**구입하자**

- 피그마 스타일 가이드
  [소개](https://www.youtube.com/watch?v=fFNeuHAL3-E)
  [스타일 가이드 소개](https://www.youtube.com/watch?v=_7wO8CZEwBw)
  [구매링크](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbEJHSVk3LU5Qc2JLeVFwLXNQa1ZHSnBDcTBmQXxBQ3Jtc0trRkV1S0lGZnlfZjJnME1fdmh6eWdXLWRjVXZvS2FJMU52WHhONVFKcEhxSG5CeFM2VTdTV05fY3VhbVBDSnJmSUdheVp2SHlyLVlkaE85M1pDc2NiTGNRYURGY1BZTVdwdXpOZDlKRUVpX1h2TWY0aw&q=https%3A%2F%2Fwww.sixshop.com%2Fmadia%2Fproduct%2Fuiux_Styleguide_2)

#### 웹 개발에 자주쓰이는 해상도

- desktop _(1920 x 1080)_

  - 일명 FHD(Full High Definition)라고 불리는 해상도
  - 24인치 데스크탑, 보편적인 모니터 크기의 해상도
  - XD 나 figma 에서 작업시 뷰포트는 1053px
  - 브라우저의 상단 부분의 URL 과, 북마크 등의 높이값을 빼고 작업을 한다. viewport는 약, 1053px 정도로 설정
  - **다만,** 배경 등 레이아웃 요소들을 1920기준으로 하되, 본문 등 실제 내용은 1000px정도로 작업


- laptop _(1536 x 864)_

  - FHD 노트북의 해상도
  - 1920 x 1080에 125% 배율을 적용한 해상도
  - 노트북 폰트 사이즈 기본 세팅이 125%로 되어 있는경우가 대부분
  - 브라우저의 상단 부분의 URL 과, 북마크 등의 높이값을 빼면 viewport는 약, 845px 정도로 설정


- tablet _(1024 x 768)_

  - 스마트폰 기기의 해상도는 너무 다양하기 때문에 모든 기준을 맞추지는 못함
  - 일반적 표현되는 해상도 기준에 맞춰 "media" 테그를 활용하여 반응형 웹으로 제작


- mobile _(375 x 667)_

  - 375 x 667 해상도는 iPhone 6, 7, 8, X 등의 기본 해상도


> [!ye] 해상도에 맞게 미디어 쿼리 작성 방법 예시
>
> _1920px은 기본값으로 별도의 미디어 쿼리 설정 없이 사용_
>
> ```css
> p{
>     font-size: 1.2rem;
> }
> @media (max-width: 1536px) {
>     p{
>         font-size:1rem;
>     }
> }
> /* 1280 ~ 1536 해상도 부터 body의 font-size가 1.2rem  */
> @media (max-width: 1280px) {
>     p{
>         font-size:0.8rem;
>     }
> }
> @media (max-width: 1023px) {
> }
> @media (max-width: 766px) {
> }
> ```

#### Reference

[css 학습 채널](https://www.youtube.com/@lundeveloper)
[youtube: how to design with figma? (A)](https://www.youtube.com/watch?v=6YpAl-U1ASU)
[youtube: how to design with figma? (B)](https://www.youtube.com/watch?v=h1gtRXskgoY)
[youtube: how to design with figma? (C)](https://www.youtube.com/watch?v=l3A9OcUd_Us)

[!re] ai 붐 이후 많이 바뀌었을텐데, 현시점에도 의미있는 근본적인 내용인지 판단 필요



### 유용한 무료 api

- 영영사전:

  발음, 예문, 동의어, 반의어 등 다양한 정보를 제공한다.
  ['darkness' 사전 검색 요청](https://api.dictionaryapi.dev/api/v2/entries/en/darkness)

### 학습법

- 무언가를 배우는 방법

  > 1. 공부 하려는 주제에 대해 조사한다.
  > 2. 설명을 적어 본다. 가상의 10살 어린이를 앞에 두고.
  > 3. 잘 모르는 부분, 지나치게 복잡한 부분(전문용어를 사용했다던가)을 찾는다.
  > 4. `3.`에서 찾은 내용을 더 쉽고 단순하게 설명하기 위해 추가적으로 조사한다.

### 아무거나 아이디어

> [!ye] 독서모임
>
> - 모두 다 읽어오는것은 아니고, 책 소개를 보고, 듣고 자세히 이야기 나누고 싶은 사람들이 모이는것.
> - 참가비가 있다. 그러나 이것은 일종의 펀딩이다.
>   따라서 동일 기간 host로 참여하고 싶은 사람들끼리 경쟁을 벌이는 것이 아니다. 충분한 대기열이 발생하면
>   펀딩에 성공할 뿐이다.

> [!ye] 조직문화를 판매한다. 그것을 뒷받침 할 수 있는 system과 합쳐서.
>
> 충북프로메이커센터에서 그런 일을 할 수 있지 않을까? 나의 목적에 담을 수 있을 것 같다.
>
> - 핵심: 실무자와 관리자 사이에 있었던 어려움을 해결하는것
>
>   내가 겪었던 어려움은,
>
>   1. 실무자인 내가 모르는 사이에 결정된 사안들이 자꾸만 나타나는것. 그리고 그것이 늦게 알려지는것.
>   2. (레퍼런스가 없을 수록)어떤 일들은 명확하게 역할이나 권한을 사전 정의 및 분배하기 어렵다. 그래서 진행하면서 눈치봐가며 하게되는데, "1.번"의 문제가 일어나면 그 선이 그어지는거다. 이게 차라리 한번 정해지면 그에 맞춰서 일하면 되는데, 이랬다가 저랬다가 바뀌니까 어느장단에 춤을 출지 너무 어려운거다.
>
> - How to: milestone이라고 부른 것을 공유하는 방식으로
>
>   1. 실무자는 업무 계획을 미리 작성해서 승인받고
>   2. 관리자와 함께 결정해야하거나 검토받아야 하는 사안을 미리 정해둔다.
>   3. 그 외에는 모두 실무자가 자율적으로 진행한다.
>   4. (예상치 못한 이슈는 당연히 예외고)


> [!ye] 메뉴얼 수행 추적기
>
> 1. 처음 생각은 workflow를 추적 할 수 있는 앱이었다. 관리자가 정의하고 실무자가 수행 기록을 남기는 것.
> 2. 근데 workflow는 너무 범위가 넓다. 수행하는 사람의 이해도나 유연함에 따라 결과물이 달라질 여지가 크다. 소비자들이 일관된 효용을 얻기가 매우 힘들것이다.
> 3. 그래서, 사용자들이 고민의 여지가 없이 상명하복을 따르되, 중간에 실수가 없고 일정에 지연이 없도록만 도와줄 수 있는 **메뉴얼로 범위를 한정** 시켜 보자.


> [!ye] 어떤거 만들어볼만한지는 awesome-selfhosted에 적당히 괜찮은 주제들이 있어요


> [!ye] 게임 아이디어
>
> [요런 스타일](https://www.youtube.com/watch?v=YPowsippmRM) 좋다.
>
> [로그라이크류](https://www.youtube.com/watch?v=JFAzdybohUc)도 재밌다.


> [!ye] 유아(6세 이하 초등입학 전)용 동화 읽어주고 각종 질문에 대답해주는 인형
>
> - 비용 절감
> - 어린이 레벨에 맞는 안전한 대화
> - 하드웨어
> - 마케팅


> [!ye] 정서, 심리 등 과학적 근거를 바탕으로 하는 검사 컨텐츠를 생성형 인공지능으로 서비스하기
>
> - 정적인 질문지와 답변을 제공하는 것이 아니라, 사용자와의 상호작용을 통해 맞춤형 진단을 제공할 수 있다.


### 구체화된 프로젝트 아이디어

[ anki-diary](/Project/ideas/anki-diary)
[ 충북프로메이케센터 홈페이지 리뉴얼](/Project/ideas/충북프로메이케센터_홈페이지_리뉴얼)

### 영화

#### 보고싶은

| 제목                   | 감독         |
|------------------------|--------------|
| Cool World             | Ralph Bakshi |
| 흔들리는 도쿄          | 봉준호       |
| 해리 프리그의 비밀전쟁 |              |
| 감자심포니             |              |

#### 관심 인물

- 곤 사토시

```txt
지나가다 글남기네요 .. 곤사토시라는 감독을 평가할때 호소다마모루같은 감독을 일본에서 국민애니감독으로 치켜세우지만 진짜 최고의 감독은 곤사토시였다라고 생각될정도로 그가남긴 작품들이 가진 영상미 음악 스토리 작화 편집까지 뭐하나 빠지지않고 지적유희를 주었던 감독이었지요


그중 곤사토시의 상상력과 그가 말하고자하는 메세지들이 가장 장 표현된것중 하나인 tv애니시리즈로 나왔던 망상대리인 이었습니다 파프리카에서도 양면성을 말하지만 망상대리인에서도 양면성 군중심리 인간이 가진 독특한 심리를 너무나도 잘풀어내던 감독이었죠 가장 현실감을 잘표현하던 감독이라는 칭호또한 그가 가진 가장큰 능력이었기때문이었을겁니다


곤사토시 작품들이 호불호가 갈리는 이유또한 알게모르게 철학이 난무하기때문일겁니다 하지만 이것을 이해만 할수있다면 정말로 신세계를 경험하게되는 경험을 하게되기도하죠


곤사토시가 어려운 작품만 만든것은 아닙니다 크리스마스에 기적을 만날 확률 같은 작품들도있기에 한번쯤 곤사토시 작품들을 찾아보는것을 꼭 추천합니다


작품 목록 남깁니다~메모리즈 부터 오하요까지는 감상할수있습니다~


<노인 Z>(老人Z, Roujin Z, 일본, 1991, 애니메이션, 레이아웃/원화)
<월드 아파트먼트 호러>(ワールドアパートメントホラー, World Apartment Horror, 일본, 1991, 애니메이션, 원안)
<조조의 기묘한 모험>(ジョジョの奇妙な冒険, JoJo's Bizarre Adventure, 일본, 1994, 애니메이션, 원화/시나리오/콘티)
<메모리즈>(メモリーズ, Memories, 일본, 1995, 옴니버스 애니메이션, 각본/콘티)
<퍼펙트 블루>(PERFECT BLUE, Perfect Blue, 일본, 1997, 애니메이션)
<천년여우>(千年女優, Millennium Actress, 일본, 2001, 애니메이션)
<크리스마스에 기적을 만날 확률>(東京ゴッドファーザーズ, Tokyo Godfathers, 일본, 2003, 애니메이션)
<망상대리인>(妄想代理人, Paranoia Agent, 일본, 2004, 애니메이션)
<파프리카>(パプリカ, Paprika, 일본, 2006, 애니메이션)
<오하요>(オハヨウ, Good Morning, 일본, 2007, 단편 애니메이션)
<꿈꾸는 기계>(夢みる機械, Dreaming Machine, 일본, 미완성, 애니메이션)
```


### 냠냠

#### 가고싶은 식당

  - 냉동숙성회 무한리필

    > 59,000 / 1인
    > 상호 : 스이센 광교
    > 주소 : 경기 수원시 영통구 이의동 1305-1
    > 전화번호 : 0507-1337-0525
    > [네이버플레이스](https://naver.me/GoDDD2wY)
    >
    > [빙결회 온라인 구매 링크](https://naver.me/G7VVmy7s)
    >
    > *예약은 10석까지, 나머지 8석은 오시는 순서대로 식사 가능합니다.
    >
    > 사장님의 저서
    > [‘주문하신 인생 한 그릇 나왔습니다‘ 책 구매 링크](https://buly.kr/Csi4fo7)


### 국내 여행

#### 수영 스팟

  - 충북 제천시 백운면 애련로 855


### 음악

- 최유리, 숲

### etc

[건전지 사용 제품을 충전지 방식으로 개조하기](https://www.youtube.com/watch?v=-zltotyU1Ek)
[커뮤니티 구축 방법론](https://www.jianyang.co.kr/p/7-feat?utm_source=publication-search)
[better way to manage dot files](https://www.youtube.com/watch?v=tBoLDpTWVOM)
[self hosted secrets manager](https://www.youtube.com/watch?v=7t5M4FXqs9E&list=WL&index=29)
[e-commerce example site](https://contents.clayful.store/)


## Archive

[칭찬 수집기 개발](/Project/진행중/칭찬_수집기/index.md)
[2025 우테코 지원](/Archive/2025_우테코_지원)

