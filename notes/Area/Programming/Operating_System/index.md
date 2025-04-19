# 󰏢 injex


## 설치&복제 [-] 

[부팅디스크 만들기](/Area/Programming/Operating_System/설치&복제/부팅디스크_만들기)
[만자로 시스템 복사(이사)하기](/Area/Programming/Operating_System/설치&복제/만자로_시스템_복사)
[Booting process](/Area/Programming/Operating_System/설치&복제/Booting_process)
  > [-] 
  > [How Does Linux Boot Process Work?](https://www.youtube.com/watch?v=XpFsMB6FoOs)


## 서비스 관리 [-] 

[systemd](/Area/Programming/Operating_System/서비스_관리/systemd)
[sysz](/Area/Programming/Operating_System/서비스_관리/sysz)
[x11 세션에서 wayland 세션을 실행할 수 있다.-AKA session in session](/Area/Programming/Operating_System/서비스_관리/x11_세션에서_wayland_세션을_실행할_수_있다.-AKA_session_in_session)


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

## system Packages & Apps

[package manager](/Area/Programming/Operating_System/system_Packages_&_Apps/package_manager)
[mimeapps](/Area/Programming/Operating_System/system_Packages_&_Apps/mimeapps)
[xdg-open](/Area/Programming/Operating_System/system_Packages_&_Apps/xdg-open)


## Shell & Terminal & Editor [-] 

[command history](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/command_history)
[what is shell?](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/what_is_shell?)
[Vim](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/Vim)
[Git](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/Git)
[alacritty config](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/alacritty_config)
[bash](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/bash)
[tmux](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/tmux)
[curl](/Area/Programming/Operating_System/Shell_&_Terminal_&_Editor/curl)


[-] 공부하자
[bash-shell, sed, awk, make, macro: 한글 가이드 문서](https://mug896.github.io/bash-shell/bug_reports.html)

[-] 
[visidata](https://www.visidata.org/)
- csv, sql 등 cli에서 데이터를 시각화하고 편집할 수 있는 툴

[-] 
[navi](https://github.com/denisidoro/navi)
- cli 명령어를 미리 입력해두고 편하게 불러와서 사용


## secretes 관리 [-] 

[-] 
[self hosted secrets manager](https://www.youtube.com/watch?v=7t5M4FXqs9E&list=WL&index=29)
[secure auth for everything](https://www.youtube.com/watch?v=N5unsATNpJk)


## etc

[`HOME` path](/Area/Programming/Operating_System/etc/HOME_path)
[GNU stow](/Area/Programming/Operating_System/etc/GNU_stow)
[bore](/Area/Programming/Operating_System/etc/bore)
  - simple tcp tunnel CLI. make localhost to public server
  - 이거 활용하면 SSH로 작업중인 서버의 개발서버를 원격지에서 접속해볼 수 있겠다.
    - 근데 어차피 hot-reloading 안되니 소용없나?
!
!
[talon: speach to text](/Area/Programming/Operating_System/etc/talon)
