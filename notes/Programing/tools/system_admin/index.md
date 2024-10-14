# 󰏢 System admin


## TOC

[sysz](/Programing/tools/system_admin/sysz.md): `systemctl`을 편리하게!

- x11 세션에서 wayland세션을 실행할 수 있다.(session in session)

  weston DE를 활용해서 가능한데,

  ```bash
  # weston 설치
  sudo pacman -S weston

  # background 실행
  weston --backend=x11-backend.so
  ```


## One day, maybe?

- 리눅스 종합 system configurations 관리 툴(아직 한창 개발중)
  [제작자 소개](https://www.youtube.com/watch?v=Zt0HnIMbzZQ)
  [github](https://github.com/ChrisTitusTech/linutil)

