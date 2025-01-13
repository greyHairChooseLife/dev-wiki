# 󰏢 Operating System


> [!td]2025-01-13
> [-] 보고 정리하기... [How Does Linux Boot Process Work?](https://www.youtube.com/watch?v=XpFsMB6FoOs)
>  󱞪


## booting up

> [!lg] Log 2025-01-13
>
> 지금까지 나는 x서버를 사용하면 모두 `$HOME/.xinitrc`을 실행하는줄 알았다. 주의깊게 한번 살펴본
> 적도 없지만 말이다.(당장 내게 중요한 것도 아니니까.)
>
> 근데 그게 아니었다. `startx` 커맨드를 실행하면 이를 실행하지만, 내 경우엔 Window Manager를 직접
> 실행하기보단 Display Manager를 사용중이었다. 그중에서도 LightDM이란걸 사용중이었고, 이놈은
> `.xinitrc`를 사용하는것이 기본값이나 관습이 아니었다.


- ~기존에 알고있던틀린 상식~
  1. ~부팅이 되는 과정에서 내가 모르는 과정으로 X서버 시작~
  2. ~X서버가 시작되면 시스템 수준 및 `$HOME` 수준에서 `.xinitrc`를 실행~
  3. ~이때 `.Xresources`도 있으면 실행~

- LightDM을 사용하는 내 시스템

  1. systemd로 관리되는 서비스의 하나로 LightDM이 등록됨

     ```bash
     systemctl status display-manager.service  # enabled
     systemctl status lightdm.service          # enabled
     ```

  2. LightDM은 관련 설정파일을 참고하여 `session-wrapper(스크립트)`를 실행.
     `session-wrapper`는 `/etc/lightdm/Xsession`인데, 이것은 `lightdm-conf`에 옵션으로 설정할
     수 있다.

     - 관련 설정 파일

       ```bash
       # DM이 사용하는 greeter 설정
       /etc/lightdm/some-greeter.conf

       # auto-login, guest-session 허용 여부 따위를 설정한다.
       # 어떤 윈도우 매니저를 사용하는지도 여기서 설정할 수 있다.
       /etc/lightdm/lightdm.conf

       # lightdm.conf에서 지정된 Window Manager에 대한 명세
       /usr/share/xsessions
         i3-with-shmlog.desktop
         i3.desktop
       ```

     - [Xsession 스크립트의 상세 내용](Xsession_스크립트의_상세_내용)

       주요내용을 살펴보면, 아래와 같다.

       - $SHELL 환경변수를 확인한 뒤 그에 맞는 쉘을 'posix모드를 끄고, 로그인 쉘'로 다시 실행한다.
         또한 .xprofile, .Xresources 등 각종 추가 설정을 세션에 sourcing 한다.

         정확한 목록은,

         - system-wide or user-level `.xprofile`
         - system-wide or user-level `.Xresources`
         - system-wide or user-level `.Xkbmap 혹은 .Xmodmap`
         - `/etc/X11/xinit/xinitrc.d` 디렉토리에 있는 모든 스크립트
         - `$HOME/.xsession` 스크립트

     - **개별적인 추가 스크립트를 위해서는 홈 디렉토리에 `.xsession`을 만들어 사용하면 되겠다.**





