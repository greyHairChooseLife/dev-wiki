# 󰏢 awesome command history



## 커맨드라인

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

### 내 ip 확인, my ip

- `curl ifconfig.me`
