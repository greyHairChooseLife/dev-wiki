---
title: '만자로 리눅스 이사하기(새로운 하드웨어)'
birth: '2023-06-23'
modified:
slug: '2023/06/moving-majaro-linux-to-new-hardware'
---

# install packages

- xbanish
- nvim
- github-cli
- yay google-chrome

# install nodes and npm

check nodejs version at https://nodejs.org/en

`sudo pacman -S nodejs-lts-hydrogen` // For 18.X version

[reference](https://wiki.archlinux.org/title/node.js)

`sudo pacman -S npm`
`npm install -g npm@latest` // For latest version

[reference](https://docs.npmjs.com/try-the-latest-stable-version-of-npm)

# ~/.bashrc

it is all about alias

# ~/.i3/config

it is my own fitted keybinds

# ~/.config/nvim

:Packer sync
:Coc install

# ~/.Xresources

it is about my termial emulator

## perl extentions

`URxvt.perl-ext-common: default,url-select,keyboard-select`

- url-select
- resize font

# fonts

`yay -S d2coding`
`yay -S ttf-hack-nerd`

직접 설치 할 수도 있지만 그러면 인식이 잘 안되더라. 아마도 권한 관리를 해 줘야 하는 것 같은데, 잘 안나오니까 그냥 편리하게 패키지 관리자를 쓰자.

# Korean input

`pacman -S scim`
`yay -S scim-hangul`

```
# ~/.xprofile

export XMODIFIERS="@im=SCIM"
export GTK_IM_MODULE=scim
export QT_IM_MODULE=scim
scim -d
```

[reference](https://dgkim5360.tistory.com/entry/basic-setup-of-korean-environment-for-arch-linux)

# git & github-cli

.gitconfig
gh authentication

# ntp로 시간 정확히 얻기

<details>
  <summary>manually setting CLI</summary>
  <div style="background-color: grey; padding: 10px; color: black">
  
This is just a quick article one how to change the timezone for Manjaro Linux.

1. Check the current time status by running:

   `timedatectl status`

2. Setting the NTP from Time

   `timedatectl set-ntp true`

3. Verify the time zones available

   `timedatectl list-timezones`

4. Set the time zone

   `timedatectl set-timezone <Time zone>`

- Example: `timedatectl set-timezone America/Chicago`

5. Check the Status to verify that the time zone set

   `timedatectl status`

Great! So you have manually set the timezone on Manjaro linux!

  </div>
</details>

[reference](https://ksummersill.medium.com/manually-change-the-time-on-manjaro-3e470616ee3c)

# timeshift

snapshot을 찍어놓는 방식으로 시스템 config 상태를 저장 해 준다.

언제 또 문제가 생길지 모르니까 설정 해 주는게 좋다. 신기하게도 시스템 관리 파일들만 골라서 백업한단다.

`pacman -S timeshift`

`sudo timeshift --create --comments "A new backup" --tags D`

(--tags D가 daily를 뜻한단다. O로 on-demand 등 그냥 말그대로 태그다.)

`sudo timeshift --list`, `sudo timeshift --restore` 등 직관적인 명령어들로 구성됐다.

# trouble-shooting

<details>
<summary>xdg-open doesn't run with google-chrome</summary>
  <div style="background-color: grey; padding: 10px; color: black">
  
```
~/.config/mimeapps.list
// comments with '#'

// FROM this
##x-scheme-handler/http=userapp-Pale Moon.desktop
##x-scheme-handler/https=userapp-Pale Moon.desktop
##x-scheme-handler/ftp=userapp-Pale Moon.desktop

// TO this
x-scheme-handler/http=google-chrome-stable.desktop
x-scheme-handler/https=google-chrome-stable.desktop
x-scheme-handler/ftp=userapp-Pale Moon.desktop

```
  </div>
</details><br>
```
