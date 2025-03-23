# 󰏢 x11 세션에서 wayland 세션을 실행할 수 있다.-AKA session in session




weston DE를 활용해서 가능한데,

```bash
# weston 설치
sudo pacman -S weston

# background 실행
weston --backend=x11-backend.so
```
