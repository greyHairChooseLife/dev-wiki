# 󰏢 awesome command history


## awk

```bash
# 디렉토리 내 모든 json파일의 길이의 총 합
jq '. | length' *.json | awk '{s+=$1} END {print s}'
```


## docker

```bash
# 컨테이너 내부 파일을 호스트로 복사
docker cp a7614153bd78:/dist/assets/index-C6b_ONX_.js prod.js
```
