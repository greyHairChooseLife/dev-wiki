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



## (step by step) Arch Linux + Windows 듀얼 부트 설치 가이드 (UEFI 기준)

### 1. 준비 단계
- **윈도우 설치 및 백업**: 기존 데이터 백업
- **Arch Linux 설치 USB 준비**: 공식 이미지로 USB 제작



### 2. 윈도우 파티션 축소
- 윈도우에서 "디스크 관리" 실행
- C: 드라이브(윈도우 파티션) 우클릭 → "볼륨 축소"
- Arch Linux용으로 사용할 만큼의 "할당되지 않은 공간" 확보



### 3. Arch Linux 설치 환경 부팅
- USB로 부팅, 첫 번째 메뉴(Arch Linux install medium) 선택



### 4. 디스크 및 파티션 구조 확인
```bash
lsblk
```
- 윈도우 파티션, EFI 파티션, 할당되지 않은 공간 확인



### 5. UEFI 부팅 확인
```bash
ls /sys/firmware/efi
```
- 결과가 있으면 UEFI, 없으면 BIOS(레거시)



### 6. Arch Linux용 파티션 생성
```bash
cfdisk /dev/nvme0n1
```
- "Free space"에서 "New"로 파티션 생성
  - 루트 파티션(예: 150GB, Linux filesystem)
  - 스왑 파티션(예: 4GB, Linux swap)
- "Write"로 저장, "Quit"로 종료



### 7. 파티션 포맷 및 마운트
```bash
mkfs.ext4 /dev/nvme0n1p5      # 루트 파티션
mkswap /dev/nvme0n1p6
swapon /dev/nvme0n1p6
mount /dev/nvme0n1p5 /mnt
mkdir -p /mnt/boot/efi
mount /dev/nvme0n1p1 /mnt/boot/efi   # EFI 파티션
```



### 8. 기본 시스템 설치
```bash
pacstrap /mnt base linux linux-firmware vim nano
```



### 9. fstab 생성
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```



### 10. chroot로 진입
```bash
arch-chroot /mnt
```



### 11. 로케일 설정
```bash
nano /etc/locale.gen      # ko_KR.UTF-8 UTF-8 또는 en_US.UTF-8 UTF-8 주석 해제
locale-gen
echo "LANG=ko_KR.UTF-8" > /etc/locale.conf
```



### 12. 타임존 설정
```bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
hwclock --systohc
```



### 13. 호스트네임 및 네트워크 설정
```bash
echo "archlinux" > /etc/hostname
nano /etc/hosts
# 아래 내용 추가
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain archlinux
```



### 14. root 비밀번호 설정
```bash
passwd
```



### 15. 일반 사용자 계정 생성 및 sudo 권한 부여
```bash
useradd -m sy
passwd sy
pacman -S sudo
usermod -aG wheel sy
EDITOR=nano visudo
# %wheel ALL=(ALL:ALL) ALL 주석 해제
```



### 16. 부트로더(GRUB) 설치 및 설정 (UEFI)
```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
echo 'GRUB_DISABLE_OS_PROBER=false' >> /etc/default/grub
pacman -S os-prober
os-prober
grub-mkconfig -o /boot/grub/grub.cfg
```



### 17. initramfs 생성 (실수로 실행해도 무방)
```bash
mkinitcpio -p linux
```



### 18. 설치 환경 종료 및 재부팅
```bash
exit
umount -R /mnt
reboot
```
- USB 제거



### 19. 첫 부팅 및 확인
- GRUB 메뉴에서 Arch Linux와 Windows가 모두 보이는지 확인
- root 또는 일반 사용자 계정으로 로그인



### 참고/문제 해결

- 윈도우가 GRUB에 나타나지 않으면 os-prober, grub-mkconfig를 다시 실행
- 네트워크, 그래픽, 사운드 등 추가 설정은 Arch Wiki 참고
