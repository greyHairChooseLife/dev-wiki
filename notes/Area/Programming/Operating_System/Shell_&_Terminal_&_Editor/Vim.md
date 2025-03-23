# 󰏢 Vim



## log history

> [!lg] Log 2024-09-11
> 뭘 써야할지 모르겠다. 어차피 neovim config는 별도 저장소로 관리하는데, 이렇게 따로 위키까지 존재할 필요가 있을까?

> [!lg] Log 2024-10-18
> 그렇지가 않다. 이 에디터를 사용하면서도 익혀야할 개념 등 학습 내용이 있다.
> 이것들은 처음엔 본말전도 아닌가 싶어도, 결국은 내가 코딩의 세계를 근본적으로 이해하는데 큰 역할을 한다. 예를들면 LSP의 존재나, 팔팔하게 살아 숨쉬는 수많은 플러그인 저장소가 운영되는 모습 등 말이다.
>
> 또한, 이곳에는 에디터를 유지보수하는데 유용한 팁을 기록하기도 하지만 보다 주요하게는 workflow를 고도화하는 의미도 크겠다. vim config를 개선해가며 결국 느낀 것은, 에디터의 설정을 기능별로 나누기보단 workflow별로 나누는 것이 더 효율적이라는 것이다. 어떤 workflow들을 가지고 있는지 정리하고 그에 동원되는 기본 기능이나 플러그인을 모아두는 것이다.


## workflows

_PR review, commit, PR, 프로젝트파일 관리 등 다양한 workflow를 나열하고, 그에 필요한 내장 기능과 플러그인 및 키맵을 정리하자._

### code navigation

_ThePrimeagen이라는 개발자(유투브채널 운영)의 방식을 적용해보기로 했다.
요는, 프로젝트별로 핵심이 되는 파일 및 객체는 손에 꼽을만큼 소수라는거다. 그것들을 보좌하는 잔챙이들이 가끔 쓰이는거고._


> [!rf]
>
> [Harpoon : Vim Conference 2021](https://www.youtube.com/watch?v=Qnos8aApa9g)

### 단일 파일 읽기

- outline.nvim
- vista.vim


### fork 떠온 플러그인 관리하기

1. 포크 떠온다.
2. `packer '<내 아이디>/<플러그인 이름>'`하여 설치한다.
3. 개선이 필요할 때, `.local/share/nvim/site/pack/packer/start/`에서 이것저것 건드려 본다.
4. 작업한 커밋을 push할땐, origin을 ssh전용 링크로 바꿔야한다. http(s)방식은 이제 못쓰니까.
   커밋 히스토리는 상관없다면 remote을 새로 clone받아서 변경 내용(파일)을 복붙하고 커밋 및 push해도 간단한다.


## simple tips
> [!rf]
>
> [how to get bigger icons?](https://www.reddit.com/r/neovim/comments/1f6a3q3/what_nerd_fonts_have_bigger_icons_than_the_others/)
> [비어있는 모든 키맵](https://breezewiki.nadeko.net/vim/wiki/Unused_keys)


### 모든 keymap 확인하기

Neovim이나 Vim에서 사용자가 정의한 모든 커스텀 키 매핑을 보려면, 내장 명령어 `:map`을 사용할 수 있습니다. 이 명령은 다양한 모드(normal, insert, visual 등)에 대한 모든 매핑을 보여주며, 특정 모드에 대한 매핑만을 보려면, 모드에 맞는 명령어 변형을 사용할 수 있습니다.

| 기능                  | command |
|-----------------------|---------|
| all mode              | `:map`  |
| normal mode           | `:nmap` |
| insert mode           | `:imap` |
| visual mode           | `:vmap` |
| command-line mode     | `:cmap` |
| operator-pending mode | `:omap` |
| select mode           | `:smap` |
| terminal mode         | `:tmap` |

> ex)
> 사용자가 정의한 모든 일반 모드의 키 매핑을 보려면 `:nmap`을 입력합니다.

- 매핑의 정의 위치와 자세한 정보 보기:
  `:verbose` 키워드를 사용하면, 매핑이 정의된 파일과 줄 번호를 함께 볼 수 있습니다. 예를 들어, `:verbose nmap`은 일반 모드의 모든 매핑과 그 매핑이 정의된 위치를 보여줍니다.

- 특정 키 시퀀스에 대한 매핑 검색:
  매핑 명령 뒤에 키 시퀀스를 추가하여 해당 키 시퀀스에 대한 매핑 정보만을 필터링하여 볼 수 있습니다. 예를 들어, `<leader>a`에 대한 일반 모드 매핑을 검색하려면 `:nmap <leader>a`를 입력합니다.


> [!ye] 참고
>
> - 명령어 뒤에 입력하는 키 시퀀스는 정확해야 하며, 매핑 시 사용한 키 표기법과 일치해야 합니다. 예를 들어, `leader` 키는 보통 `<leader>`로 표기됩니다.
> - custom 설정이나 플러그인에 의해 추가된 매핑도 함께 나열됩니다.
> - `:Telescope keymap`도 가능
