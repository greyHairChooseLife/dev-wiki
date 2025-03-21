# 󰏢 command history



## network

### lookup ip

```bash
# 내부
hostname -i | awk '{print $1}'

# 외부
curl ifconfig.me

```

### wifi 관리

- 서비스명: `NetworkManager`
- `nmcli` 또는 `nmtui`

```
# 활성화 연결 확인
nmcli connection show

# wifi 네트워크 검색
nmcli device wifi list

# wifi 연결(이미 접속 정보가 저장되어 있을 경우 password 생략 가능)
nmcli device wifi connect <SSID> password <password>

# 비밀번호 확인
nmcli -s -g 802-11-wireless-security.psk connection show <SSID>
```


## directory & file

### 두 디렉토리가 완전히 일치하는지 비교

| 명령                                          | 설명                                          | 특성              |
|-----------------------------------------------|-----------------------------------------------|-------------------|
| `rsync -av --dry-run /source/ /destination/`  | 수정 날짜 & 크기 기준 비교                    | 빠름, 정확도 낮음 |
| `rsync -avc --dry-run /source/ /destination/` | `-c` 옵션은 파일 내용을 체크섬(해시)으로 비교 | 느림, 정확도 높음 |

- 출력이 없으면 두 디렉토리는 완전히 동일함.


## git & gh-cli

### organization 관련

```bash
# 모든 organization 조회
gh org list

# 특정 organization의 repository 조회
gh repo list ORG_NAME --limit 100
```

ex)
```bash
gh repo list trainspotting-big-data-projects --limit 100
gh repo list trainspotting-big-data-projects --limit 100 --json name,description,visibility | jq '.[]'
```


### git tag

- CRUD

  | 명령어                                   | 설명                                    | 주된 사용 목적                      | 예시                                            |
  |------------------------------------------|-----------------------------------------|-------------------------------------|-------------------------------------------------|
  | `git tag`                                | 로컬 태그 목록 조회                     | 생성된 태그 확인                    | `git tag`                                       |
  | `git show <태그이름>`                      | 태그 상세 정보 확인                     | 태그가 붙은 커밋 및 설명 확인       | `git show v1.0.0`                               |
  | `git tag -a <태그이름> -m "설명"`          | 주석이 포함된 태그 추가 (Annotated)     | 버전 관리 및 부연 설명 포함         | `git tag -a v1.0.0 -m "First release"`          |
  | `git tag -a <태그이름> <커밋해시> -m "설명"` | 특정 커밋에 태그 추가                   | 과거 커밋이나 특정 커밋에 태그 지정 | `git tag -a v1.0.0 3f2e9b4 -m "Stable release"` |
  | `git tag -d <태그이름>`                    | 로컬에서 태그 삭제                      | 불필요한 태그 제거                  | `git tag -d v1.0.0`                             |
  | `git push origin <태그이름>`               | 특정 태그를 원격 저장소로 푸시          | 선택된 태그만 업로드                | `git push origin v1.0.0`                        |
  | `git push origin --tags`                 | 모든 태그를 원격 저장소로 푸시          | 한 번에 전체 태그 업로드            | `git push origin --tags`                        |
  | `git push origin --delete <태그이름>`      | 원격 저장소에서 태그 삭제               | 잘못된 태그 제거                    | `git push origin --delete v1.0.0`               |


- Read 특화

  | 명령어 | 설명 | 주요 사용 목적 | 예시 |
  |--------|----------------------------------|------------------------------|------|
  | `git tag -l` | 모든 태그를 리스트 형식으로 출력 | 태그 목록 조회 | `git tag -l` |
  | `git tag -l "v1.*"` | 특정 패턴(`v1.*`)을 포함한 태그만 출력 | 원하는 태그만 필터링 | `git tag -l "release-*"` |
  | `git tag -n` | 모든 태그 + 주석(설명) 한 줄 출력 | 태그 목록과 요약 확인 | `git tag -n` |
  | `git tag -n 5` | 모든 태그 + 주석 5줄까지 출력 | 긴 주석 포함한 태그 확인 | `git tag -n 5` |
  | `git tag -l -n` | 모든 태그 + 주석(설명) 한 줄 출력 | 태그 목록 + 주석 확인 | `git tag -l -n` |
  | `git tag -l -n 3` | 모든 태그 + 주석 3줄까지 출력 | 긴 주석 포함 태그 필터링 | `git tag -l -n 3` |
  | `git tag -l -n "v1.*"` | `v1.*`으로 시작하는 태그 + 주석(1줄) 출력 | 특정 태그 패턴 + 주석 조회 | `git tag -l -n "release-*"` |
  | `git tag -l -n 5 "v1.*"` | `v1.*`으로 시작하는 태그 + 주석 5줄 출력 | 특정 태그 패턴 + 긴 주석 조회 | `git tag -l -n 5 "release-*"` |


## ETC

### 여러 필드중 하나를 선택하고, 그 다음 커맨드의 인자로 넘기기

```bash
# 방법1
command1 | cut -d ' ' -f1 | xargs command2

# 방법2
command1 | awk '{print $1}' | xargs command2

# 예시
gh gist list | awk '{print $1}' | xargs gh gist view
```



### awk

- 디렉토리 내 모든 json파일의 길이의 총 합
```bash
jq '. | length' *.json | awk '{s+=$1} END {print s}'
```


### docker

- 컨테이너 내부 파일을 호스트로 복사
```bash
docker cp a7614153bd78:/dist/assets/index-C6b_ONX_.js prod.js

ex)
docker cp index.js ps2:/usr/src/app/index.js
```

- 볼륨 위치 얻기
```
docker inspect --format "{{range .Mounts}}{{.Source}}{{end}}" stapep-gpu
```

- docker-compose.yml 위치 얻기
```
docker inspect --format '{{index .Config.Labels "com.docker.compose.project.config_files"}}' stapep-gpu
```


### image convert

- 이미지 크기 수정
```bash
# imagemagick 패키지를 이용
# 화질 구지
magick input.jpg -resize 50% output.jpg
```


### inside vim

- rows가 엄청 많을 때, json으로 변환하기 위해 양쪽을 따옴표로 감싸기
  (이게 `:norm I"` 이런식으로 하면 엄청 느리다.)

```bash
:%s/^/"/ | %s/$/"/
```


### pnpm을 npm으로 되돌리기

1. `rm pnpm-lock.yaml`
2. `rm -rf node_modules`

### puppeteer

- 프록시 설정

  ```javascript
  const browser = await puppeteer.launch({
    args: [
      '--proxy-server=http://your-proxy-server:port', // 프록시 서버 주소
    ]
  });
  ```


### pptx to image

[web](https://convertio.co/kr/pptx-png/)

### nodejs

`node --max-old-space-size=8192 index.js`


### zip, archive

- 압축하기

  `zip -r archive.zip ./dir`


### pdf 조작

- 180도 회전

  `qpdf --rotate=180 input.pdf output.pdf`

### youtube 다운로드

- 오디오만 추출

  `yt-dlp -x --audio-format mp3 <url>`

### vim buffer sort

- Acending : {'n', 'v'} `:sort`
- Decending: {'n', 'v'} `:sort!`

### 쌍모니터(가로 + 세로)

```bash
# 때때로 display가 hdmi-2~3등 다른 이름으로 잡힐 수 있다. 이러면 xrandr로 확인해서 잡아 넣어주자.
# 보통 껏다키면 실행하는데, 상기 이유로 auto-start로 등록해두진 않았다.

mons -e left && xrandr --output HDMI-1 --rotate left
```


### 사운드카드 서비스 재시작

```bash
systemctl --user restart pulseaudio

# Or with administrative (root) privileges,
# restart PulseAudio system-wide
sudo systemctl restart pulseaudio
```


### man to txt

```bash
$ man <command> | col -b > filename.txt
```


### 특정 파일 찾아서 삭제

```bash
find . -type f ! \( -name "*.png" -o -name "*.pdb" \) -delete
```


