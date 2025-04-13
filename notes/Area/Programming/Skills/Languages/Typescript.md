# 󰏢 Typescript


## What do you know?

### tsconfig.json

- `exclude`, `include`:
  컴파일 대상에서 제외/포함 할 요소 지정

- `path`:
  path alias를 매핑할 수 있도록 한다.
  ```json
  "compilerOptions": {
    "paths": {
      "@components/*": ["src/components/*"],
      "@utils/*": ["src/utils/*"]
    }
  }
  ```

- `target`:
  TS -> JS로 트랜스파일링 한 결과가 어떤 버전의 JS로 나올지 지정

- `module`:
  commonjs로 지정하더라도 import/export 구문을 사용 가능. 불러오는 모듈에 따라 require()를 쓸지 다른걸 할지 TS가 책임진다.




## Problems [-] 

### path alias


> [!qt] path alias 사용시 문제점
>   󱞪 찾아보면 그냥 tsconfig에서 baseUrl과 paths만 잘 설정해주면 된다고 하는데, 나는 아래 조치가 추가적으로 필요했다. 왜일까?
>
> ```json
> npm install -D "tsconfig-paths": "^4.2.0"
>
> ## File Path: backend/tsconfig.json, 26-28
>   "ts-node": {
>     "require": ["tsconfig-paths/register"]
>   }
> ```
