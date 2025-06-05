# 󰏢 Arch Linux 패키지 업데이트 후 X11 문제 해결


## 문제 상황

- 패키지 업데이트 후 `startx` 실행 시 에러 발생
- X11이 정상적으로 시작되지 않음


## 진단 과정

### 1. 최근 업데이트된 패키지 확인

```bash
grep -i upgraded /var/log/pacman.log | tail -20
```

### 2. X11 핵심 패키지 업데이트 이력 확인

```bash
grep -E "(xorg-server|mesa|libx11|xorg-xinit)" /var/log/pacman.log | tail -10
```

### 3. 원인 패키지 식별

- `mesa (1:25.0.5-1 -> 1:25.1.1-2)` 업데이트가 문제 원인으로 확인
- Mesa는 그래픽 드라이버의 핵심 구성요소


## 해결 방법

### Mesa 패키지 롤백

```bash
# 캐시에서 이전 버전으로 롤백
sudo pacman -U /var/cache/pacman/pkg/mesa-1:25.0.5-1-*.pkg.tar.zst

# 또는 Archive에서 다운로드 후 설치
wget https://archive.archlinux.org/packages/m/mesa/mesa-1:25.0.5-1-x86_64.pkg.tar.zst
sudo pacman -U mesa-1:25.0.5-1-x86_64.pkg.tar.zst
```

### 임시 패키지 홀드 (선택사항)

```bash
# mesa 자동 업데이트 방지
echo "IgnorePkg = mesa" | sudo tee -a /etc/pacman.conf
```


## 결과

- `startx` 정상 실행 확인
- X11 환경 복구 완료


## 참고사항

- 그래픽 관련 문제 시 `mesa`, `xorg-server` 패키지를 우선 의심
- `/var/log/pacman.log`는 패키지 문제 해결의 핵심 정보원

