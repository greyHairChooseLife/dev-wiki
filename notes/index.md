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


### 대기

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

#### 살아남는 개발자는 무엇이 다른가? [-]

> - 쉽게 변하지 않는 것들에 집중하자
> - 시키는 대로 하기 전에 의심해라. 정말로 필요한 과정/단계/방법인가? 똑똑하고 신뢰할만한 사람일수록
> - recipe를 존중하되, 본질을 반드시 이해해라

[개발자 중 누가 살아 남을까? 생존을 위해 반드시 생각해야 할 점](/Area/개발_환경/살아남는_개발자는_무엇이_다른가?/개발자_중_누가_살아_남을까?_생존을_위해_반드시_생각해야_할_점)
[개발자가 앞으로 꼭 공부해야하는 분야](/Area/개발_환경/살아남는_개발자는_무엇이_다른가?/개발자가_앞으로_꼭_공부해야하는_분야)
[일론의 5단계 법칙](/Area/개발_환경/살아남는_개발자는_무엇이_다른가?/일론의_5단계_법칙)
[일류와 이류의 차이 (반도체 전설 짐켈러)](/Area/개발_환경/살아남는_개발자는_무엇이_다른가?/일류와_이류의_차이_(반도체_전설_짐켈러))


[-]
[The LinkedIn DPH Framework](https://linkedin.github.io/dph-framework/)

[-]
[지식근로자의 생산성](https://www.joinc.co.kr/w/knowledge-workder-productivity)


#### 구독 서비스 관리

| Category     | Service        | Cost      |
|--------------|----------------|-----------|
| Subscription | GitHub Copilot | $100/Y    |
|              | OpenAI GPT     | $20/M     |
|              | Figma          | $15/M     |
| API          | Anthropic      | claude.ai |


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


[-]
[포프TV - 노트 정리 & 회사 문서화 & 프로젝트 관리, 이거면 충분!](https://www.youtube.com/watch?v=iE7N5RlM13E)

#### Remote Work

[file share](/Area/개발_역량/Remote_Work/file_share)
[NFS protocol, 디렉토리 공유하기](/Area/개발_역량/Remote_Work/NFS_protocol,_디렉토리_공유하기)
[SSH protocol](/Area/개발_역량/Remote_Work/SSH_protocol)
[rclone으로 클라우드 스토리지 서비스 동기화](/Area/개발_역량/Remote_Work/rclone으로_클라우드_스토리지_서비스_동기화)

#### Operating System [-]

##### 설치&복제 [-]

[/Area/개발_환경/Operating_System/설치&복제/부팅디스크 만들기](/Area/개발_환경/Operating_System/설치&복제/부팅디스크_만들기)
[/Area/개발_환경/Operating_System/설치&복제/만자로 시스템 복사(이사)하기](/Area/개발_환경/Operating_System/설치&복제/만자로_시스템_복사(이사)하기)
[/Area/개발_환경/Operating_System/설치&복제/Booting process](/Area/개발_환경/Operating_System/설치&복제/Booting_process)

  [-]
  [How Does Linux Boot Process Work?](https://www.youtube.com/watch?v=XpFsMB6FoOs)




##### 서비스 관리 [-]

[systemd](/Area/개발_역량/Linux_&_Shell/systemd)
[sysz](/Programing/tools/system_admin/sysz.md)
[x11 세션에서 wayland 세션을 실행할 수 있다.(session in session)](x11_세션에서_wayland_세션을_실행할_수_있다.(session_in_session))


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


#### Skills [-]

[index.md](Area/개발_환경/Skills/index.md)

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

#### 오답노트

- 미결
  [docker named volume with NFS protocol](/Area/개발_역량/오답노트/docker_named_volume_with_NFS_protocol)


- 해결
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


### 글쓰기 [-]

#### 뭐든지

[대학교를 중퇴한 이유가 뭔가요?](/Life/수필/대학교를_중퇴한_이유가_뭔가요?)
[10시 이후로는 언어정보의 input을 제한한다.](/Life/수필/10시_이후로는_언어정보의_input을_제한한다.)
[vim을 왜 쓸까?](/Programing/posts/vim을_왜_쓸까?)
[gitsigns.nvim plugin에 이슈 남기기](/Programing/posts/gitsigns.nvim_plugin에_이슈_남기기)
[nvim 플러그인에 이슈 남기기](/Programing/posts/nvim_플러그인에_이슈_남기기)
[오픈소스에 난생처음 PR 날리기](/Programing/posts/오픈소스에_난생처음_PR_날리기)
[우리는 상대방을 눈앞에 두고도 그 실체를 보지 못한다. 단지, 내 방식대로 이해한 허상을 바라볼 뿐이다.](우리는_상대방을_눈앞에_두고도_그_실체를_보지_못한다._단지,_내_방식대로_이해한_허상을_바라볼_뿐이다.)
[살아오면서 겪어본 어려운 문제에 대하여](살아오면서_겪어본_어려운_문제에_대하여)

#### 유투브 시청 [-]

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

- 부분 운동
  [팔, 10m](https://www.youtube.com/watch?v=0LWGOAtgcww)
  [가슴, 10m](https://www.youtube.com/watch?v=E7OAhVIbkbk)
  [등, 10m](https://www.youtube.com/watch?v=BGd4sKOcl5I)
  [어깨, 10m](https://www.youtube.com/watch?v=3smk7LGLTXE)
  [배, 8m](https://www.youtube.com/watch?v=J90dERWZNUE)

- 전신 운동
  [전신, 12m](https://www.youtube.com/watch?v=vl4S_e_dBuk)
  [전신, 10m](https://www.youtube.com/watch?v=8wEdqDDzHpc)
  [전신, 8m](https://www.youtube.com/watch?v=C80QyNlAMss)
  [전신, 7m](https://www.youtube.com/watch?v=g-nzEPqqrx8)
  [전신, 6m](https://www.youtube.com/watch?v=LDb5Y4Ti6Bc)
  [전신, 5m](https://www.youtube.com/watch?v=XeWwQK-I2Tg)


### 민주 시민의 책임&권한&상식

[세금 납부](/Life/democratic_citizen/index.md)
[각종 서류](각종_서류)


## Resource

### 프로젝트 아이디어

- 단순 아이디어
  [독서모임](프로젝트_아이디어/독서모임)
  [조직문화를 판매한다. 그것을 뒷받침 할 수 있는 system과 합쳐서.](프로젝트_아이디어/조직문화를_판매한다._그것을_뒷받침_할_수_있는_system과_합쳐서.)
  [메뉴얼 수행 추적기](프로젝트_아이디어/메뉴얼_수행_추적기)
  [유아(6세 이하 초등입학 전)용 동화 읽어주고 각종 질문에 대답해주는 인형](프로젝트_아이디어/유아(6세_이하_초등입학_전)용_동화_읽어주고_각종_질문에_대답해주는_인형)
  [정서, 심리 등 과학적 근거를 바탕으로 하는 검사 컨텐츠를 생성형 인공지능으로 서비스하기](프로젝트_아이디어/정서,_심리_등_과학적_근거를_바탕으로_하는_검사_컨텐츠를_생성형_인공지능으로_서비스하기)
  ["시끄러운 목소리"가 주목받는 "댓글" 시스템 개선안](프로젝트_아이디어/"시끄러운_목소리"가_주목받는_"댓글"_시스템_개선안)


- 구체화된 아이디어
  [anki-diary](/Project/ideas/anki-diary)
  [충북프로메이케센터 홈페이지 리뉴얼](/Project/ideas/충북프로메이케센터_홈페이지_리뉴얼)


#### TIPS

- 어떤거 만들어볼만한지는 awesome-selfhosted에 적당히 괜찮은 주제들이 있어요

- 게임 아이디어
  [요런 스타일](https://www.youtube.com/watch?v=YPowsippmRM) 좋다.
  [로그라이크류](https://www.youtube.com/watch?v=JFAzdybohUc)도 재밌다.


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

### 학습법

- 무언가를 배우는 방법

  > 1. 공부 하려는 주제에 대해 조사한다.
  > 2. 설명을 적어 본다. 가상의 10살 어린이를 앞에 두고.
  > 3. 잘 모르는 부분, 지나치게 복잡한 부분(전문용어를 사용했다던가)을 찾는다.
  > 4. `3.`에서 찾은 내용을 더 쉽고 단순하게 설명하기 위해 추가적으로 조사한다.

### 대중문화

[보고싶은 영화 리스트](보고싶은_영화)
[인물](인물)
[음악](음악)



### 국내 여행

[가고싶은 식당](여행/가고싶은_식당)



### 차량 관리

- 청주시 서원구 기후대기과:
  - 043)201-4985
  - 2024/04/29 17:30 쯤 전화통화

- 청주시 차량등록사업소 세무팀
  - 043)201-4951
  - 2024/04/29 15:37 전화통화

- 올드카 성지: 이화카센터


[비용 기록](/Resource/차량_관리/비용_기록)
[베터리 자가교체](/Resource/차량_관리/베터리_자가교체)
[정기검사지연 과태료와 운행정지명령](/Life/car/정기검사지연_과태료와_운행정지명령.md)
[청주시의 미세먼지 비상저감조치](/Life/car/청주시의_미세먼지_비상저감조치.md)




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




## Archive

[칭찬 수집기 개발](/Project/진행중/칭찬_수집기/index.md)
[2025 우테코 지원](/Archive/2025_우테코_지원)
[우정한사랑 이사](/Archive/우정한사랑_이사/index.md)
[gatsby로 블로그](/Archive/gatsby로_블로그/index.md)
