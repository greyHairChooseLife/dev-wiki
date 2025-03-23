# 󰏢 GNU stow




_나도 이제는 config 관리를 해 줘야하는 것이 나름 많아졌다. 예를들면 neovim, bash, alacritty, tmux, github ...
각각 저장소 또는 gist로 관리하고 있었는데, 이제는 한계인 것 같다._

_stow는 각종 도구들의 config을 하나의 저장소에서 관리하고, symlink를 활용해서 적절한 위치로 쏴주는 거라고 들었다._



## init


> [공식홈페이지](https://www.gnu.org/software/stow/)에서 아래와 같이 소개하고 있다.
>
> GNU Stow is a symlink farm manager which takes distinct packages of software and/or data located
> in separate directories on the filesystem, and makes them appear to be installed in the same
> place. For example, /usr/local/bin could contain symlinks to files within
> /usr/local/stow/emacs/bin, /usr/local/stow/perl/bin etc., and likewise recursively for any other
> subdirectories such as .../share, .../man, and so on.
>
> This is particularly useful for keeping track of system-wide and per-user installations of
> software built from source, but can also facilitate a more controlled approach to management of
> configuration files in the user's home directory, especially when coupled with version control
> systems.
>
> Stow is implemented as a combination of a Perl script providing a CLI interface, and a backend
> Perl module which does most of the work. Stow is Free Software, licensed under the GNU General
> Public License.


알고있던게 맞는 듯 하다.


> [!qt] GNU?
>
>   󱞪 '자유 소프트웨어 운동'을 지원하기 위해 1980년대에 시작된 프로젝트다. 완전히 자유로운 오픈소스
>   운영체제를 만드는 것이 목표이다. **'GNU' is Not Unix**의 약자라고 한다.
>
>   󱞪 왜 이렇게 Unix에 집착하는걸까? Unix는 macOS/Linux의 (설계)기반이 되는 운영체제로,
>   모듈화/텍스트데이터 중심/다중 사용자/tree구조로 계층적인 파일 시스템을 특징으로 한다. 그러나
>   상업적 목적으로 개발되어, 자유롭게 사용할 수 없었다. 이에 반대하여 만들었으므로 자유
>   소프트웨어라는 측면을 강조하고, 기술적으로는 유사하나 상표권 따위를 침해하지 않으리라는 명확한
>   의사표시라고 한다.


> [!qt] symlink farm manager?
>
>   󱞪 'Symlink farm'과 'manager'로 구분해서 이해해야 맞다. farm은 뭔가 똑같은게 잔뜩 모여있는걸
>   의마하는데, symlink가 잔뜩 모여있고 이것을 관리하기 때문에 저렇게 부른다.



### 유투브로 둘러보기

[ref1](https://www.youtube.com/watch?v=y6XCebnB9gs&t=79s)

- `$HOME/dotfiles/`을 생성하고 해당 디렉토리를 `stow` 하면, 디렉토리 안에 모든 파일의 symlink가
$HOME/`으로 생성된다.
- `.git/` ignored by default
- make `.stow-local-ignore` file to ignore files
  - 이때 dir을 표현한다고 끝에 `/`를 붙이면 안된다. (ex. `vim/` -> `vim`)

- stow package(stow로 관리하는 파일/디렉토리)와 동일한 path를 가진 plain text file이 이미
  존재한다면, conflict 오류가 발생한다. 해당 파일을 지우던지 --adopt로 덮어쓰던지 해야한다. 물론
  '이미 존재하는 파일' 쪽의 내용을 '입양'해 오는 것이지, 그 반대가 아니다.

  다만, stow로 관리하는 디렉토리는 보통 git으로 버전관리 하니까 괜찮다.



## 사용

바로 사용해 보자.

[dotfiles](https://github.com/greyHairChooseLife/dotfiles)
