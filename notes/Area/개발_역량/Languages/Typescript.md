# 󰏢 Typescript


#### tsconfig.json

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
