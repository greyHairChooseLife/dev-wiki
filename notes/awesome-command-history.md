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
