# 󰏢 arch linux



> [!lg] Log 2025-05-13
>
> _"I use arch, btw."_
 

> [!rf]
>
> https://wiki.archlinux.org/title/Installation_guide
> https://data-science.tistory.com/281






아치위키나 다른 블로그 살펴보며 따라하면 아주 간단하다. 다만, 보다 최적화/개인화 하기위해 아래를
반영해 보자.


## 개선 사항

### 파티션 나누기
   
```bash
# 현재 상태 확인
fdisk -l

# 파티셔닝
# cfdisk는 TUI 프로그램으로 사용하기 쉽다.
cfdisk /dev/nvme0n1
```


- 이미 설치된 기존 OS를 유지하려면 EFI 파티션을 그냥 둬야한다. arch wiki를 보면 1G를 할당하는
  것을 권하는데, 나는 300M만 할당돼 있었다. 이때 기존 파티션을 그대로 두고 사용하라고 안내되는데,
  사이즈를 더 키울수도 없다.
 
- 동시에 여러개의 커널/OS를 사용하는 경우를 위해 넉넉히 잡는 듯 한데, 통상 100M정도가 필요하다한다.

  
### 기본 패키지 설치

```bash
pacstrap /mnt base linux linux-firmware vim neovim intel-ucode

pacman -S grub efibootmgr networkmanager sudo git stow
```


- 나머지는 개인 설정파일에서 로딩하는게 좋을듯

### i3 window manager + 개인화

```bash
git clone https://github.com/greyhairchooselife/dotfiles
cd dotfiles && bash stow.sh
# pacman -S xorg xorg-xinit i3-wm i3status i3lock alacritty 
# echo "exec i3" > ~/.xinitrc
```

