# 󰏢 awesome command history


## awk

- 디렉토리 내 모든 json파일의 길이의 총 합
```bash
jq '. | length' *.json | awk '{s+=$1} END {print s}'
```


## docker

- 컨테이너 내부 파일을 호스트로 복사
```bash
docker cp a7614153bd78:/dist/assets/index-C6b_ONX_.js prod.js

ex)
docker cp index.js ps2:/usr/src/app/index.js
```


## image convert

- 이미지 크기 수정
```bash
# imagemagick 패키지를 이용
# 화질 구지
convert input.jpg -resize 50% output.jpg
```


## inside vim

- rows가 엄청 많을 때, json으로 변환하기 위해 양쪽을 따옴표로 감싸기
  (이게 `:norm I"` 이런식으로 하면 엄청 느리다.)

```bash
:%s/^/"/ | %s/$/"/
```


## pnpm을 npm으로 되돌리기

1. `rm pnpm-lock.yaml`
2. `rm -rf node_modules`

## puppeteer

- 프록시 설정

  ```javascript
    const browser = await puppeteer.launch({
      args: [
        '--proxy-server=http://your-proxy-server:port', // 프록시 서버 주소
      ]
    });
  ```


## pptx to image

[web](https://convertio.co/kr/pptx-png/)

## nodejs

`node --max-old-space-size=8192 index.js`


## zip, archive

- 압축하기

  `zip -r archive.zip ./dir`
