#  DEV WIKI


> _이 위키의 목적은 기록하여 필요시 되찾아보기 위함이다.
> 누군가에게 뽐내기 위한 용도가 아니다._


> _"더 열심히 해"_ from.고은상


> _프로덕션에 가까운 프로젝트들을 보면서 배우는 것이 중요하다._
> _"Fediverse가 프로덕션에 가깝게 되어있는게 많아요. 루비는 마스토돈, nodejs는 미스키, 파이썬은 bookwyrm"_


> _Make note? Do PARA!_
>
> PARA 메모법:_실행도를 기준으로 노트를 분류하는 기법. 주제별로 나누는 것이 아니다._
>
>   - Project:
>     - **지금 가장 전념**하는 것
>     - 기간/목표가 명확함. 즉, 끝이 있음
>     - ex) 이사하기, 직장 취업하기, 책 쓰기
>
>   - Area:
>     - **끝이 없고, 꾸준히** 해 나가는 것
>     - 나의 책임이나 역할이 있음
>     - ex) 운동, 독서, 프로그래밍 실력 향상
>
>   - Resource:
>     - 관심 분야 정보
>     - 내 **책임은 없음**
>     - ex) 창업, 종교, 예술, 춤추는법 등
>
>   - Archive:
>     - 위의 세 가지에 속하지 않는 것
>     - 종료/중단된 프로젝트
>     - 책임이 사라진 영역, 관심이 사라진 자원



[temp](temp)


## Project


[CBPM 견적서 관리 웹앱 개발](/Project/CBPM_견적서_관리_웹앱_개발/index.md)

[결혼에 확신을 갖는 방법 찾기](/Project/결혼에_확신을_갖는_방법_찾기/index.md)

[성금 엄마아빠집 리모델링](/Project/성금_엄마아빠집_리모델링/index.md)


[만다라트](/Project/만다라트/index.md)
  - 2차전에 돌입했다.


- 차량 관리
  - 코란도 엔진오일
  - 경비 아저씨가 나 대신 ..
  - 관련해서 스트레스를 준 아저씨가 있다. 골프장 아저씨



- neovim `tohtml.vim` 개선

  > `render-markdown.nvim`으로 렌더링(하이라이팅) 된 것을 있는 그대로 pdf 또는 이미지로 출력하고싶었다.
  > `TOhtml` 명령을 이용해보란 조언을 얻었다.(vim.kr 디스코드 채널)
  >
  > 그런데 vimwiki에서 당장 TOhtml 해보면 에러가 발생하는거다. 이것에 대해 대화하다가 아래와같은 조언을
  > 얻었다.
  >
  > ```txt
  > tohtml이 루아로 변환된지 오래되지 않아서 어쩌면 네오빔 업스트림에 기여하실수도있어요
  > 버그 고치신다면 리뷰하고 받아주실거같아요
  > html 나온거에서 </pre> 에 해당하는 부분
  > 그 부분 계산하다가 태그가 안맞아서 뻗은거네요
  > <pre> .. </pre> 사이에 태그가 뭔가 빠졌다는건데
  > ```
  >
  > [tohtml.vim](https://github.com/neovim/neovim/blob/3d3a99e69cda365cae9ad65831712301807a772b/runtime/lua/tohtml.lua)


## Area [-]

### 개발 환경 [-]

#### 살아남는 개발자는 무엇이 다른가?

- 쉽게 변하지 않는 것들에 집중하자
- 시키는 대로 하기 전에 의심해라. 정말로 필요한 과정/단계/방법인가? 똑똑하고 신뢰할만한 사람일수록
- recipe를 존중하되, 본질을 반드시 이해해라


##### TODO

[The LinkedIn DPH Framework](https://linkedin.github.io/dph-framework/)
[지식근로자의 생산성](https://www.joinc.co.kr/w/knowledge-workder-productivity)

##### READ

[개발자 중 누가 살아 남을까? 생존을 위해 반드시 생각해야 할 점](/AREA/개발_역량/살아남는_개발자는_무엇이_다른가?/READ/개발자_중_누가_살아_남을까?_생존을_위해_반드시_생각해야_할_점)
[개발자가 앞으로 꼭 공부해야하는 분야](/AREA/개발_역량/살아남는_개발자는_무엇이_다른가?/READ/개발자가_앞으로_꼭_공부해야하는_분야)
[일론의 5단계 법칙](/AREA/개발_역량/살아남는_개발자는_무엇이_다른가?/READ/일론의_5단계_법칙)
[일류와 이류의 차이 (반도체 전설 짐켈러)](/AREA/개발_역량/살아남는_개발자는_무엇이_다른가?/READ/일류와_이류의_차이_(반도체_전설_짐켈러))


#### 구독 서비스 관리

- subscribe
  - github copilot: $100/Y
  - openAI: gpt: $20/M
  - figma: $15/M

- API
  - anthropic: claude.ai

- AWS


#### Note Making [-]

[Job-wiki 작성법](/Area/개발_역량/Note_Making/Job-wiki_작성법)

> [!INFO]
>
> [vim-wiki cheet-sheet](https://gist.github.com/drkarl/4c503bccb62558dc85e8b1bc0f29e9cb)
> [design reference](https://mkaz.blog/)
> - 현재 디렉토리 내의 파일명에 공백이 있을 때, 공백을 '_'(under-bar)로 변경하기
>   ```bash
>   for file in *\ *; do mv "$file" "${file// /_}"; done
>   ```


##### TODO

[포프TV - 노트 정리 & 회사 문서화 & 프로젝트 관리, 이거면 충분!](https://www.youtube.com/watch?v=iE7N5RlM13E)

#### Remote Work

[file share & control](/Cheat_Sheet/file_share_&_control)
[nfs 프로토콜, 디렉토리 공유하기](/Programing/workflows/서버관리/nfs로_디렉토리_공유하기)
[SSH protocol](/Programing/workflows/서버관리/SSH)
[rclone으로 클라우드 스토리지 서비스 동기화](/Programing/workflows/cloud_storage/rclone으로_클라우드_스토리지_서비스_동기화)

#### Operating System [-]

##### 설치/복제 [-]

[부팅디스크 만들기](/Area/개발_환경/Operating_System/부팅디스크_만들기)
[만자로 시스템 복사(이사)하기](/Area/개발_역량/Linux_&_Shell/만자로_시스템_복사하기)
[Booting process](2.Area/개발환경/Operating_System/index.md) [-]

##### 서비스 관리

[systemd](/Area/개발_역량/Linux_&_Shell/systemd)
[sysz](/Programing/tools/system_admin/sysz.md)
[x11 세션에서 wayland 세션을 실행할 수 있다.(session in session)](x11_세션에서_wayland_세션을_실행할_수_있다.(session_in_session))

##### system Packages/Apps

[package manager](/Area/개발_환경/Operating_System/Manage_Packages)
[mimeapps](/Cheat_Sheet/System_configs)
[xdg-open](/Area/개발_환경/Operating_System/xdg-open_관리)

##### Shell & Terminal & Editor [-]

[command history](/Cheat_Sheet/command_history)
[what is shell?](/Area/개발_역량/Linux_&_Shell/what_is_shell)
[Vim](/Programing/tools/vim/index.md)
[Git](/Programing/tools/git_and_github/index.md)
[alacritty config](/Programing/tools/terminal_emulator/alacritty.md)
[bash](2.Area/개발환경/bash/index.md)
[tmux](/Area/개발_환경/Operating_System/tmux)


[-] 공부하자
[bash-shell, sed, awk, make, macro: 한글 가이드 문서](https://mug896.github.io/bash-shell/bug_reports.html)

[-]
[visidata](https://www.visidata.org/)
- csv, sql 등 cli에서 데이터를 시각화하고 편집할 수 있는 툴

[-]
[navi](https://github.com/denisidoro/navi)
- cli 명령어를 미리 입력해두고 편하게 불러와서 사용


##### secretes 관리 [-]

[-]
[self hosted secrets manager](https://www.youtube.com/watch?v=7t5M4FXqs9E&list=WL&index=29)
[secure auth for everything](https://www.youtube.com/watch?v=N5unsATNpJk)


##### etc

[`$HOME` path](/Area/개발_역량/Linux_&_Shell/home_path)
[GNU stow](2.Area/개발환경/stow/index.md)
[bore](/Programing/concepts_and_tools/tools/bore.md)
  - simple tcp tunnel CLI. make localhost to public server
  - 이거 활용하면 SSH로 작업중인 서버의 개발서버를 원격지에서 접속해볼 수 있겠다.
    - 근데 어차피 hot-reloading 안되니 소용없나?





##### TODO

[-] bluetooth 문제를 해결하며...
> 갑자기 블루투스가 안됐다. systemctrl에서 bluetooth.service는 running 상태로 잘 확인 됐다.
> 근데 bluetoothctl에서 확인해보니 "org.bluez.Error.NotReady" 이러면서 connect도 안되고 scan도 안된다.
> 그래서 검색해보니 뭔 soft block 되었는지 확인하란다.
> https://unix.stackexchange.com/questions/508221/bluetooth-service-running-but-bluetoothctl-says-org-bluez-error-notready
>
> ```
> ~$ rfkill list
> 0: ideapad_wlan: Wireless LAN
> 	Soft blocked: no
> 	Hard blocked: no
> 1: ideapad_bluetooth: Bluetooth
> 	Soft blocked: yes
> 	Hard blocked: no
> 2: ideapad_3g: Wireless WAN
> 	Soft blocked: no
> 	Hard blocked: no
> 3: phy0: Wireless LAN
> 	Soft blocked: no
> 	Hard blocked: no
> 4: hci0: Bluetooth
> 	Soft blocked: yes
> 	Hard blocked: no
>
> ~$ rfkill unblock all
> ```
>
> 이렇게 하니 잘 된다. 이게 뭘까? 한번 알아보자.
>
>
> [!re] 다만...
>
> 아래처럼 `Failed to connect: org.bluez.Error.Failed br-connection-page-timeout`같은 에러
> 메시지가 나올 땐 안통하는 방법이다... 일단은 그냥 재부팅 하자.
>
> ```
> [HHKB-Hybrid_1]# connect EC:2C:73:72:19:01
> Attempting to connect to EC:2C:73:72:19:01
> [HHKB-Hybrid_1]# Failed to connect: org.bluez.Error.Failed br-connection-page-timeout
> ```







#### AI [-]


[-]
[“AI는 없는 인간만의 뭐가 있다? 그런 말 하는 사람 다 밀려났다.” (장강명 작가)](https://www.youtube.com/watch?v=bBaTc1JE42w)


##### 사용중

- [aider](/Area/개발_환경/AI/aider)
- avante.nvim
- github copilot with ChatCopilot.nvim
- chatGPT web UI

> [!lg] Log 2025-03-19
>
> avante.nvim vs ChatCopilot.nvim?
> : AI api provider에 직접 접근하냐, github에서 랩핑해서 배포하는걸 사용하냐의 차이
> 비용적으로는 copilot이 우위가 있으나 성능은 또 모르겠다. 일단은 지켜보는 수밖에?

##### 관심

- devin(commercial) & [devica(open-source)](https://github.com/stitionai/devika/discussions)
  : agent-like

- codecompanion.nvim
  : zed ai를 표방한다는데, 'cursor AI' 스타일보다 vim스럽긴 하다고 생각한다. 그러나 ai의
  활용 면에서 나는 cursor AI 스타일을 선호한다.

##### ETC

[what is chatGPT?](/Area/개발_환경/AI/what_is_chatGPT?)


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

#### Skills

##### Container

[docker](/Area/개발_역량/Container/docker)

[HUB: kubernetes](/Programing/concepts_and_tools/tools/kubernetes/index.md)

[-]
[from docker to open-source](https://www.youtube.com/watch?v=Z5uBcczJxUY)

##### Web Dev [-]

[static vs dynamic website](/Area/개발_역량/Web_Dev/static_website_vs_dynamic_website)
[design](/Area/개발_역량/Web_Dev/design)


[-]
[천재들이 만든 웹의 구조는 실제로 이렇게 동작합니다.](https://www.youtube.com/watch?v=po5_T3wSPJI)

[-]
- 제대로 된 에러핸들링 전략이 있어야겠다.

  `try ... catch ...` 구문은 연쇄적으로 에러를 추적한다. cascade error handling이라고 부를 수 있겠다.
  따라서 (바깥쪽)가장 먼저 에러를 이르킨 지점부터 (안쪽)가장 깊은 지점까지 단계별로 에러를 추적하며
  에러메시지가 누적 로깅 된다. 이때 어떻게 연쇄적으로 throw시키고, 어떤 에러객체를 전달할지 정해진
  전략이 필요하다.

  - HTTP 상태코드
    | code | 이름                  | 설명                                           |
    |------|-----------------------|------------------------------------------------|
    | 401  | Unauthorized          | 인증이 필요하거나 실패했을 때.                 |
    | 403  | Forbidden             | 인증은 되었지만, 자원에 접근할 권한이 없을 때. |
    | 500  | Internal Server Error | 서버 내부 문제.                                |

[-] 2024-12-05
- What is preflight request?

  비표준 http 헤더를 사용하는 경우 보안을 위해 미리 요청을 보내본다는데... 이와 관련된 캐싱 정책도
  개발자가 고려해야할 상항이다. 관련된 내용을 자세히 알아두자.

[-] 2024-12-06
- typescript+express 서버에서 컨트롤러를 만들 때, early return으로 res객체를 보낼 때가 있다. 뭔가
에러가 있다거나 경우에 따라 다른 응답을 보내야 할 때다. 근데 해당 컨트롤러의 마지막까지 실행되지
않고 응답한다면, 이후의 코드는 실행되지 않도록 return 해줘야한다. 근데 이게 `res.status().json()`
자체를 리턴하는것과 이후 void를 리턴하는 것에 차이가 있다.(당연하다..) 근데 문제는 이게 다르다는게
아니라 일관되지 않다는거다. `return res.status().json()`을 하면 에러가 날 때가 있고, 아닐때가 있다.
typescript의 기본 request, response객체 타입에 어긋난게 에러의 원인이라는데, 에러의 원인과는 별개로
왜 일관되지 않은 결과가 나타나는가?

  - 사례
    ```typescript
    export const getFileSignedUrl = async (req: AuthRequest, res: Response) => {
      try {
        // 인증 확인
        if (!req.userInfo) {
          res.status(401).json({ message: '인증되지 않은 사용자입니다.' });
          return;
        }

        const fileKey = decodeURIComponent(req.params.fileKey);

        if (!fileKey) {
          res.status(400).json({ message: '파일 키가 필요합니다' });
          return;
        }

        // 파일 존재 여부 및 접근 권한 확인 로직 추가 필요
        const presignedUrl = await awsS3Service.getPresignedFileUrl(fileKey);

        res.json({ presignedUrl });
      } catch (error) {
        console.error('파일 서명 URL 생성 오류:', error);
        res.status(500).json({
          message: '파일 URL 생성 중 알 수 없는 오류가 발생했습니다.',
        });
      }
    };
    ```


##### Chrome Extension

> [!lg] Log 2025-02-27
> surfingkeys라는걸 잘 쓰고있었는데, 최근 업데이트 후 문제가 생겨서 직접 버전을 다운그레이드
> 해야했다.
>
> [관련 이슈](https://github.com/brookhong/Surfingkeys/issues/2244)
>
> 타인이 배포하는 프로젝트를 내 맘대로 다운그레이드 할 수는 없고, 소스코드를 받아서 원하는 버전으로
> checkout 한 뒤, 직접 빌드했다.(원작자가 npm 스크립트를 미리 작성해둬서 편리했다.)
>
> 브라우저에서 제공하는 load unpacked 기능을 사용하니, 원하는대로 동작했다.
>
> 이런식으로 직접 빌드한 도구를 사용하면 더 간결하게 이것저것 만들 수 있겠다.


> [!rf]
> [따라하기: chrome todo extension in JS](https://www.youtube.com/watch?v=ny-L_KLrKIU)

##### Cloud Computing

[AWS](/Area/개발_역량/Cloud_Computing/AWS)

##### CI CD

_배포 자동화는 고객의 요구사항을 빠르게 반영하는 핵심 요소이다. 요구사항을 파악하고
빠르게 반영하기도 바쁜데, 배포가 매끄럽지 못해서 턱턱 막힌다면 진짜 갑갑하다._

[single container SPA](/Area/개발_역량/CI_CD/single_container_SPA)

##### Languages [-]

[Javascript](/Area/개발_역량/Languages/Javascript) [-]
[Dart](/Area/개발_역량/Languages/Dart)

- Python
  [기본개념](/Area/개발_역량/Languages/Python)
  [격리된 가상환경으로 개발 시작하기](/Area/개발_환경/Python/격리된_가상환경으로_개발_시작하기)

    > [!INFO]
    >
    > 파이썬은 널리 이용되는 만큼 생태계가 복잡하다. 따라서 아래 두가지가 중요하다.
    >   - 의존성 관리를 잘 해줘야한다.
    >   - 시스템 패키지를 해치지 않도록 주의해야한다.
    >
    > 따라서 나는 가상환경의 확실한 격리를 컨테이너를 활용하여 시도한다.

##### Web Scraping

[general tips](/Area/개발_역량/Web_Scraping/general_tips)

[-]
[Scrape Anything with DeepSeek V3 + Scraping Tool Integration (CHEAP & EASY)](https://www.youtube.com/watch?v=WkLdLJJzV1k&t=27s)


##### Big Data

[Keywords](/Area/개발_역량/Big_Data/Keywords)

> [!rf]
>
> [free-ebook: "Data Wrangling with JavaScript"](https://www.manning.com/books/data-wrangling-with-javascript?a_aid=datawranglingwithjavascript&a_bid=acc654f9)
> [빅데이터 플랫폼 특론: play-list](https://www.youtube.com/playlist?list=PLCsebpDZm0n6HYSDaNxKQYrNrD4Xk9meX)
> [하둡 분산파일 시스템 구축: play-list](https://www.youtube.com/watch?v=g6xIMSYjh0w&list=PLY-_9hx4ldZwYOjtfRT0MV2k9JcnTUYW2)

##### Database [-]

- RDBMS
  > [!rf]
  > [강의 재생목록](https://www.youtube.com/playlist?list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe)

  [관계형 데이터베스의 종류](/Area/개발_역량/Database/RDBMS/관계형_데이터베스의_종류)
  [기본개념 및 키워드](/Area/개발_역량/Database/RDBMS/기본개념_및_키워드)
  [SQL 기본개념, Attribute Type, Key Constraints](/Area/개발_역량/Database/RDBMS/SQL_기본개념,_Attribute_Type,_Key_Constraints)
  [SQL query](/Area/개발_역량/Database/RDBMS/SQL_query)
  [SQL에서 NULL의 의미](/Area/개발_역량/Database/RDBMS/SQL에서_NULL의_의미)
  [SQL과 JOIN](/Area/개발_역량/Database/RDBMS/SQL과_JOIN)
  [SQL과 정렬 및 통계](/Area/개발_역량/Database/RDBMS/SQL과_정렬_및_통계)
  [데이터베이스 트랜잭션, ACID](/Area/개발_역량/Database/RDBMS/데이터베이스_트랜잭션,_ACID)
  [데이터베이스 설계-정규화](/Area/개발_역량/Database/RDBMS/데이터베이스_설계-정규화)
  [데이터베이스 설계-DB index](/Area/개발_역량/Database/RDBMS/데이터베이스_설계-DB_index)
  [데이터베이스 설계-파티셔닝, 샤딩, 레플리케이션](/Area/개발_역량/Database/RDBMS/데이터베이스_설계-파티셔닝,_샤딩,_레플리케이션)
  [데이터베이스 성능 최적화-서버 리소스 활용](/Area/개발_역량/Database/RDBMS/데이터베이스_성능_최적화-서버_리소스_활용)
  [데이터베이스 성능 최적화-DB connection pool](/Area/개발_역량/Database/RDBMS/데이터베이스_성능_최적화-DB_connection_pool)
  [RDB vs NoSQL](/Area/개발_역량/Database/RDBMS/RDB_vs_NoSQL)

- ETC
  [snake_case to camelCase](/Area/개발_역량/Database/ETC/snake_case_to_camelCase)


[-]
- SQL concurrency control, 트랜잭션의 Isolation 보장하기
  : serial schedule만 사용한다면 데이터 일관성을 해칠일은 없겠지만 성능 이슈가 있다.
    big data를 다룬다면 성능을 위해 none-serial schedule을 실행할 수 있도록 반드시 익혀두자.
    > [!rf]
    > [개념 강의](https://www.youtube.com/watch?v=DwRN24nWbEc&list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe&index=15)




#### 오답노트

##### 미결

[docker named volume with NFS protocol](/Area/개발_역량/오답노트/docker_named_volume_with_NFS_protocol)

##### 해결

[domain status: clientHold](/Area/개발_역량/오답노트/domain_status:_clientHold)
[프로세스 조회 및 종료](/Area/개발_역량/오답노트/프로세스_조회_및_종료)

#### REFERENCE

- ETC
  [7 Habits For Effective Text Editing](https://www.youtube.com/watch?v=eX9m3g5J-XA)
  [양질의 블로그](https://www.joinc.co.kr/w/architecture)
  [`TS`, `python` 등 다양한 주제 튜토리얼](https://www.squash.io/tutorials/)
  [The twelve-factor app](https://12factor.net/)
  [Harvard CS 50 (2023) – Full Computer Science University Course](https://www.youtube.com/watch?v=LfaMVlDaQ24)

- backend course
  [web-course](https://www.boot.dev/tracks/backend)
  [github](https://github.com/bootdotdev/curriculum)



### 좋은 가족, 좋은 친구, 멋진 인간 되기

[선물 하고싶은 것](/Life/좋은_가족,_좋은_친구_되기/index.md)
[감사한 마음](감사한_마음)
[다른 입장을 이해해 보기](다른_입장을_이해해_보기)


### 글쓰기

#### 뭐든지

[대학교를 중퇴한 이유가 뭔가요?](/Life/수필/대학교를_중퇴한_이유가_뭔가요?)
[10시 이후로는 언어정보의 input을 제한한다.](/Life/수필/10시_이후로는_언어정보의_input을_제한한다.)
[vim을 왜 쓸까?](/Programing/posts/vim을_왜_쓸까?)
[gitsigns.nvim plugin에 이슈 남기기](/Programing/posts/gitsigns.nvim_plugin에_이슈_남기기)
[nvim 플러그인에 이슈 남기기](/Programing/posts/nvim_플러그인에_이슈_남기기)
[오픈소스에 난생처음 PR 날리기](/Programing/posts/오픈소스에_난생처음_PR_날리기)
[우리는 상대방을 눈앞에 두고도 그 실체를 보지 못한다. 단지, 내 방식대로 이해한 허상을 바라볼 뿐이다.](우리는_상대방을_눈앞에_두고도_그_실체를_보지_못한다._단지,_내_방식대로_이해한_허상을_바라볼_뿐이다.)
[살아오면서 겪어본 어려운 문제에 대하여](살아오면서_겪어본_어려운_문제에_대하여)

#### 유투브 시청

['아들 학폭' 복수 나선 아빠, 판사도 "그럴 만했네.." 무죄 (2024.12.21/MBC뉴스)]('아들_학폭'_복수_나선_아빠,_판사도_"그럴_만했네.."_무죄_(2024.12.21/MBC뉴스))


[-]
[사랑하는 방법(팔란티어 접근 방식)](https://www.youtube.com/watch?v=zyXgqQkWULU)
- 이 사람은 구독하고 잘 봐야겠다.
- 심지어 투자를 하고싶은 마음까지 들게 만든다.

[-]
[조건을 안보기 시작한 IT 업계](https://www.youtube.com/watch?v=M8oj2ZIoYbY)


#### REFERENCE

[글쓰기 레퍼런스 모음](글쓰기_레퍼런스)




### 나를 사랑하기

#### 식사


#### 운동

[어깨+덤벨](https://youtube.com/shorts/rYiAk3zY5-0?si=F6K7vnZNwltDYwMh)

### 차량 관리

[Car](/Life/car/index.md)

### 민주 시민의 책임&권한&상식

[세금 납부](/Life/democratic_citizen/index.md)
[각종 서류](각종_서류)




## Resource

### Design

#### Tools

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
> - 참가비가 있다. 그러나 이것은 일종의 펀딩이다. 따라서 동일 기간 host로 참여하고 싶은 사람들끼리
>   경쟁을 벌이는 것이 아니다. 충분한 대기열이 발생하면 펀딩에 성공할 뿐이다.

> [!ye] 조직문화를 판매한다. 그것을 뒷받침 할 수 있는 system과 합쳐서.
>
> 충북프로메이커센터에서 그런 일을 할 수 있지 않을까? 나의 목적에 담을 수 있을 것 같다.
>
> - 핵심: 실무자와 관리자 사이에 있었던 어려움을 해결하는것
>
>   내가 겪었던 어려움은,
>
>   1. 실무자인 내가 모르는 사이에 결정된 사안들이 자꾸만 나타나는것. 그리고 그것이 늦게 알려지는것.
>   2. (레퍼런스가 없을 수록)어떤 일들은 명확하게 역할이나 권한을 사전 정의 및 분배하기 어렵다.
>      그래서 진행하면서 눈치봐가며 하게되는데, "1.번"의 문제가 일어나면 그 선이 그어지는거다. 이게
>      차라리 한번 정해지면 그에 맞춰서 일하면 되는데, 이랬다가 저랬다가 바뀌니까 어느장단에 춤을
>      출지 너무 어려운거다.
>
> - How to: milestone이라고 부른 것을 공유하는 방식으로
>
>   1. 실무자는 업무 계획을 미리 작성해서 승인받고
>   2. 관리자와 함께 결정해야하거나 검토받아야 하는 사안을 미리 정해둔다.
>   3. 그 외에는 모두 실무자가 자율적으로 진행한다.
>   4. (예상치 못한 이슈는 당연히 예외고)


> [!ye] 메뉴얼 수행 추적기
>
> 1. 처음 생각은 workflow를 추적 하는 앱이었다. 관리자가 정의하고 실무자가 수행 기록을 남기는 것.
> 2. 근데 workflow는 너무 범위가 넓다. 수행하는 사람의 이해도나 유연함에 따라 결과물이 달라질 여지가
>    크다. 소비자들이 일관된 효용을 얻기가 매우 힘들것이다.
> 3. 그래서, 사용자들이 고민의 여지가 없이 상명하복을 따르되, 중간에 실수가 없고 일정에 지연이
>    없도록만 도와줄 수 있는 **메뉴얼로 범위를 한정** 시켜 보자.


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


> [!ye] "시끄러운 목소리"가 주목받는 "댓글" 시스템 개선안
>
> - 기존 시스템 분석
>   - 사람들은 보통 타인의 댓글을 보고 나서 자기 댓글을 쓴다.
>   - 이 과정에서 자신의 생각이 오염될 수 있다.
>   - 사회적 동물인 우리는 여론을 무시할 수 없기 때문이다.
>   - 한편, 우리는 '주관'이 중요하다 여기면서도 '사람들'의 생각은 어떠한지 궁금해한다.
>
> - 개선안
>   - 남들이 쓴 댓글을 보려면 반드시 자신의 댓글을 써야 한다.
>   - 작성된 댓글들의 내용을 분석하여 여론을 분석 결과를 제공한다.



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
| 버팔로 66              |              |
| Big Night, 1996        |              |
| 도그빌                 |              |

#### 관심 인물

- 곤 사토시

  ```txt
  지나가다 글남기네요 .. 곤사토시라는 감독을 평가할때 호소다마모루같은 감독을 일본에서
  국민애니감독으로 치켜세우지만 진짜 최고의 감독은 곤사토시였다라고 생각될정도로 그가남긴 작품들이
  가진 영상미 음악 스토리 작화 편집까지 뭐하나 빠지지않고 지적유희를 주었던 감독이었지요


  그중 곤사토시의 상상력과 그가 말하고자하는 메세지들이 가장 장 표현된것중 하나인 tv애니시리즈로
  나왔던 망상대리인 이었습니다 파프리카에서도 양면성을 말하지만 망상대리인에서도 양면성 군중심리
  인간이 가진 독특한 심리를 너무나도 잘풀어내던 감독이었죠 가장 현실감을 잘표현하던 감독이라는
  칭호또한 그가 가진 가장큰 능력이었기때문이었을겁니다


  곤사토시 작품들이 호불호가 갈리는 이유또한 알게모르게 철학이 난무하기때문일겁니다 하지만 이것을
  이해만 할수있다면 정말로 신세계를 경험하게되는 경험을 하게되기도하죠


  곤사토시가 어려운 작품만 만든것은 아닙니다 크리스마스에 기적을 만날 확률 같은 작품들도있기에
  한번쯤 곤사토시 작품들을 찾아보는것을 꼭 추천합니다


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
[good programmer](https://parksb.github.io/article/32.html)

> [강력추천! 영상 크리에이터라면 꼭 알아야 할 7가지 웹사이트]([https://www.youtube.com/watch?v=yYwJI9y_arE)
>
> === Timestamps ===
> 00:00 인트로
> 00:23 레퍼런스 사이트1
> 01:12 레퍼런스 사이트2
> 02:02 레퍼런스 사이트3
> 02:22 레퍼런스 사이트4
> 02:49 레퍼런스 사이트5
> 03:22 레퍼런스 사이트6
> 03:56 레퍼런스 사이트7
> 03:56 아웃트로
>
> ▶레퍼런스 사이트 링크들 입니다.
> https://eyecannndy.com/
> https://kive.ai/discover
> https://film-grab.com/
> https://shot.cafe/
> https://www.cosmos.so/
> nytimes.com/column/anatomy-of-a-scene

### 연구용 ai

- ResearchRabbit: 관련 논문을 찾아주고, 관계도를 시각적으로 표현해줌



## Archive

[칭찬 수집기 개발](/Project/진행중/칭찬_수집기/index.md)
[2025 우테코 지원](/Archive/2025_우테코_지원)
[우정한사랑 이사](/Archive/우정한사랑_이사/index.md)
[gatsby로 블로그](/Archive/gatsby로_블로그/index.md)
