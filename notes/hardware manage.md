- 쌍모니터(가로 + 세로)

  `mons -e left && xrandr --output HDMI-1 --rotate left`

  > 때때로 display가 hdmi-2~3등 다른 이름으로 잡힐 수 있다. 이러면 xrandr로 확인해서 잡아 넣어주자.
  > 보통 껏다키면 실행하는데, 상기 이유로 auto-start로 등록해두진 않았다.

- soundcard restart

  `systemctl --user restart pulseaudio`

  (If you have administrative (root) privileges and want to restart PulseAudio system-wide, you can use the following command instead:

  `sudo systemctl restart pulseaudio`


- 설치된 패키지 용량 순으로 확인

  `expac -H M "%011m\t%-20n\t%10d" $(comm -23 <(pacman -Qqe | sort) <(pacman -Qqg base base-devel | sort)) | sort -n`
