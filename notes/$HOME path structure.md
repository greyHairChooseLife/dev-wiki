---
title: '$HOME path structure'
date: 2024-04-22
update:
---

home directory에도 사진, 다운로드, 도큐먼트 뭐 이런것만 있지는 않다.

내가 이미 사용중인것만 해도 `~/.config/nvim` 위치에 설정파일이 nvim 설정파일이 있다. 다른 많은 프로그램들의
설정파일이 이곳에 위치한다.

그리고 나는 종종 neovim 플러그인의 세부 내용을 열어보는데, 그 위치가
`~.local/share/nvim/site/pack/packer/start/`이다. 이 안에 내가 packer로 설치한 모든 플러그인이 있다.

오늘은 이 path가 특별하게 보였는데, `.local/` 안에 얼마전 내가 fork고 수정해서 사용하는 스크립트를
저장해두기 적절해보여서 그렇다.

## 1. $HOME/.local/

시스템의 다른 사용자에게 영향을 미치지 않는 영역이다.  
_($HOME/ 하위에 존재하니 당연한가?)_

> 참고로 `pacman` 패키지 매니저로 어플을 설치하면 "/usr/" 위치에 설치된다. pacman은 **시스템 패키지
> 매니저**이기 때문이다.

### 1-1. 하위 디렉토리

> 아래가 주요하지만 전부는 아니다.

1. ~/.local/bin

   사용자가 직접 스크립트나 실행 가능 파일을 설치할 때 사용되는 디렉토리. (반드시 직접 설치하지 않아도)다른
   패키지가 설치되는 과정에서 여기에 실행파일이 설치되기도 한다.

   > 내 경우엔 각종 python 스크립트와 glow라는 프로그램이 있었다.  
   > glow는 cli에서 markdown을 렌더링해주는 프로그램인데, 이건 내가 설치한게 확실하다. 그러나 python
   > 스크립트는 자동 생성된 것.

2. ~/.local/share

   user app의 데이터 저장. 예를 들어, 여러 어플리케이션의 설정이나 nvim의 플러그인 따위다. 어플마다 디렉토리
   하나씩 가지고 필요에 따라 활용한다.

   > 여기엔 훨씬 다양한 프로그램 관련 디렉토리가 있었다. 삭제한 프로그램의 관련 데이터도 남아있어서 그렇다.
   > 프로그램을 삭제할 때, 때때로 관련 '데이터가 모두 삭제됩니다'라고 안내하는 경우가 있는데, 이런 부분까지
   > 청소해준다는 뜻이었구나.  
   > chrome의 확장 프로그램이나 기타 vim의 플러그인, android 에뮬레이터 관련 정보 따위가 있다. 에뮬레이터는
   > 모델마다 따로 다운받은 것으로 기억하는데, 그 디바이스 정보가 여기에 저장되는 듯 하다. 또, 신기해서
   > 깔아봤던 kiwi-talk라는 3rd party 카카오톡 클라이언트에서는 db까지 저장하고 있다.
   >
   > 특히 눈에 띈 것은 pnpm의 캐싱 디렉토리가 있다는 것이다. pnpm/store/v3/files아래에 16진수로 된
   > 디렉토리들이 있고, 검색해보니 내가 사용한 패키지들이 정말로 저장되어있다. pnpm을 처음 쓰기 시작할 때,
   > _"훨씬 빠르다더라", "진짜 빠르네!"_ 정도의 생각밖에 못했는데, 이런 특징이 한몫했구나 싶다.

3. ~/.local/state

   어플리케이션의 상태값이 저장돼있다.

   > nvim 에디터의 경우 cursor jump, mark, change등 상태값이 저장되어있다. 단일 파일인데 어떻게 관리되는지
   > 정확히는 모르겠다. 또 swap파일이나 plugin의 이용 정보가 저장되기도 하고.. lsp 접속 로그나 파일
   > 디스크립터\* 관련 에러가 발생했을 때 로그도 여기에 저장된다.
   >
   > > File Descriptor(FD)는 프로그램이 파일에 접근할 때 사용하는 핸들러다. stdin, stdout, stderr같은 것들
   > > 말이다. 예전에 c언어 잠깐 볼 때를 기억해!! 하여튼, vim editor에서 파일에 접근 시 어떤 FD 관련 에러가
   > > 발생했는지 로그가 저장되는 것.
   >
   > 블루투스 정보나 pnpm 자체의 update 기록도 여기에 저장되어있다.

4. etc

   이 밖에도 잡다구리한게 있다. 주로 사용하는것은 위 3가지 정도로 보인다.

   - ~/.local/etc
   - ~/.local/lib

## 2. XDG Base Directory Specification

위의 내용은
[XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)을
따른다. 어떤 파일이 어디에 있어야 하는지에 관해 일종의 컨벤션을 제시한 것이다.

당연히 구체적인 내용보단 추상적인 내용이다. 내용중 Basics의 일부를 발췌해보면 아래와 같다.

> The XDG Base Directory Specification is based on the following concepts:
>
> - There is a single base directory relative to which **user-specific data files** should be written. This
>   directory is defined by the environment variable $XDG_DATA_HOME.
> - There is a single base directory relative to which **user-specific configuration files** should be
>   written. This directory is defined by the environment variable $XDG_CONFIG_HOME.
> - There is a single base directory relative to which **user-specific state data** should be written. This
>   directory is defined by the environment variable $XDG_STATE_HOME.
> - There is a single base directory relative to which **user-specific executable files** may be written.

굵게 표시한 부분이 위에서 소개한 path들에 해당함을 알 수 있다.

나는 기껏해야 바탕화면을 관리하지 않는 사람을 나무랄 정도인데, 이런 멋진 탑을 쌓아온 사람들도 있구나.

## 3. fork하고 customize한 프로그램을 제대로 저장하기

원래는 `$HOME/Me/forks/프로그램`에 넣어두고, 해당 path에 sh파일을 bash로 실행시켰다. 그러나 이제는 XDG Base
Directory Specification을 따라 제대로 저장해서 사용해보자.

1. forked 프로젝트를 `~/.local/share/`에 저장한다.
2. `~/.local/bin/`에 실행파일을 만든다.

   ```bash
   #!/bin/bash
   ~/.local/bin/프로그램명 "$@" # "$@"은 모든 인자를 전달한다.
   ```

3. 실행권한을 준다.

   `chmod +x ~/.local/bin/프로그램명`
