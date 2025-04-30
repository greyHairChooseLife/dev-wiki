# 󰏢 jq 사용법


> TIPS
>
> - 복잡한 jq 명령은 작은 부분으로 나눠 테스트 후 조합하세요
> - `length` 함수는 배열의 요소 수뿐만 아니라 객체의 키 개수도 반환합니다
> - `input_line_number`는 배열 인덱스보다 1이 큽니다 (0-기반 vs 1-기반)
> - 결과를 파일로 저장하려면 `> output.json`을 명령 끝에 추가하세요
> - jq는 데이터 변환과 조작에도 강력합니다 (`map()`, `reduce()` 등 활용)


## 기본 사용법

### 기본 데이터 접근
```bash
# JSON 파일 내용 예쁘게 출력
jq '.' data.json

# 최상위 배열의 각 요소 접근
jq '.[]' data.json

# 특정 필드 접근
jq '.[] | .album' data.json

# 중첩 필드 접근
jq '.[] | .album.artists' data.json
```

### 1. 키 구조만 추출
```bash
# 최상위 키만 보기
jq 'keys' data.json

# 객체 배열의 첫 항목 키 보기
jq '.[0] | keys' data.json

# 모든 객체의 키 구조를 배열로 보기
jq '[.[] | keys]' data.json
```

### 2. 중첩 구조 탐색
```bash
# 특정 경로의 구조 확인
jq '.[0].album | keys' data.json

# 모든 album 객체의 키 보기
jq '[.[] | .album | keys]' data.json
```

### 3. 스키마 추출 (복잡한 구조용)
- ```bash
  # 재귀적으로 스키마 구조 생성
  jq '
  def schema:
    if type == "object" then
      map_values(schema) 
    elif type == "array" then
      if length > 0 then [.[0] | schema] else [] end
    else
      type
    end;
  schema
  ' data.json
  ```

- ```bash
  # one-line
  jq ' def schema: if type == "object" then map_values(schema) elif type == "array" then if length > 0 then [.[0] | schema] else [] end else type end; schema ' data.json
  ```





## 데이터 필터링

### 1. 샘플 데이터 축소해서 보기
```bash
# 배열의 첫 항목만 보기
jq '.[0]' data.json

# 특정 깊이까지만 보기 (여기서는 2단계)
jq 'recurse(objects, arrays) | select(depth <= 2)' data.json
```

### select() 함수 사용
```bash
# 특정 조건에 맞는 항목 필터링
jq '.[] | select(.album.id == 123)' data.json

# 배열 길이 비교 필터링
jq '.[] | select(.album.artists | length != .artists | length)' data.json

# 복합 조건 사용
jq '.[] | select(.album.artists | length > 2 and .artists | length < 5)' data.json

# 정규식 사용
jq '.[] | select(.album.name | test("Best.*"))' data.json
```



## 집계 및 분석

### 개수 세기
```bash
# 특정 조건의 항목 개수 세기
jq '[.[] | select(.album.id == 123)] | length' data.json

# 배열 길이 확인
jq '.[] | .album.artists | length' data.json

# 중복 없는 고유값 개수
jq '[.[] | .album.id] | unique | length' data.json
```

### 불일치 확인
```bash
# album.artists와 artists 배열 길이가 다른 항목 찾기
jq '.[] | select(.album.artists | length != .artists | length)' data.json

# custom output
jq '.[] | select(.album.artists | length != .artists | length) | {index: input_line_number - 1, album_artists: (.album.artists | length), artists: (.artists | length)}' data.json
```


## 출력 형식 지정

### 커스텀 객체 출력
```bash
# 원하는 필드만 포함한 객체 생성
jq '.[] | {id: .album.id, artist_count: (.artists | length)}' data.json

# 계산된 값 포함
jq '.[] | {index: input_line_number - 1, matches: (.album.artists | length == .artists | length)}' data.json
```


## 일괄 처리

### 여러 파일 처리
```bash
# 디렉토리 내 모든 JSON 파일 처리
find /path/to/directory -name "*.json" -exec sh -c 'echo "파일: $1"; jq "[.[] | select(.album.artists | length != .artists | length)] | length" "$1"' sh {} \;

# 파일명 패턴으로 처리
for file in {1..50}.json; do
  echo "파일: $file"
  jq '.[] | .album | length' "$file"
  echo "-------------------"
done
```

### 응용 예제
```bash
# 파일별 불일치 항목 개수 보고서
for file in *.json; do
  count=$(jq '[.[] | select(.album.artists | length != .artists | length)] | length' "$file")
  total=$(jq '. | length' "$file")
  echo "$file: $count/$total 불일치"
done

# JSON 데이터 유효성 검사 스크립트
for file in *.json; do
  if ! jq 'any(.[] | .album.artists | length != .artists | length)' "$file" > /dev/null; then
    echo "$file: 모든 항목 일치"
  else
    echo "$file: 불일치 항목 있음"
  fi
done
```


