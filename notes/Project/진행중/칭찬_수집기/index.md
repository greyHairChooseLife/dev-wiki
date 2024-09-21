# 󰏢 칭찬 수집기, compliment collector

> [!cn] 개념정리
>
> - 여자친구랑 칭찬에 관해 대화하다가 나온 아이디어.
> - 어린 자녀에게 칭찬을 할 때 **결과가 아니라 과정**을 칭찬하는 것이 좋겠다는 대화였다.
> - 그런데 우리부터가 잘 못하니, 서로 하루에 하나씩 칭찬하면서 이것을 분석해보자는 결론.
>
> [배포](https://greyhairchooselife.github.io/compliment-collector/)

## Update Log [-]

[-] 하루 한번만 칭찬을 할 수 있도록 제한, 하루의 기준은 한국 기준 24:00
[-] 칭찬 기록에 시간 표현은 현지 기준 읽기 쉬운 형태로 수정(현재 UTC 기준)

## 개발 가이드라인 with chatGPT

- 요청 프롬프트:

  ```txt
  구글 sheets를 데이터베이스로 활용하는 간단한 스테틱 어플리케이션을 만들거야.
  배포는 github pages로 하고싶은데, vite, react, typescript로 프로젝트를 시작할거야.

  google sheets와 연결하고 데이터를 쓰고 읽는 api 사용이 관건이겠는데, 이 부분에 대해서는 경험이 전혀 없어.

  한번 가이드해봐

  ```

  [답변: Google_Sheets를_데이터베이스로_활용한_React_+_TypeScript_프로젝트_가이드](/Project/진행중/칭찬_수집기/Google_Sheets를_데이터베이스로_활용한_React_+_TypeScript_프로젝트_가이드)


## 제작 과정: 1차 시도 [-]

1. vite, ts, react 프로젝트 생성
2. 시트 읽기: Google Sheets API 없이

   - 시트별로 가져오기
   - SQL 문법으로 가져오기

   > [!rf]
   >
   > [video](https://www.youtube.com/watch?v=cRwpTv33Z_g)
   > [repo](https://github.com/theotrain/load-google-sheet-data-using-sql)
   > [-]  columns 값을 얻기 위해서는 fetched response의 lable이 아니라 id를 가져와야한다. 기본적으로 lable은 빈 값이기 때문에 table data를 얻기 위해 순회하는 동안 동일한 column name에 의거, row마다의 마지막 요소로 덮어씌워져 버린다. 이것을 softfork 및 PR날려보자. 유투브 영상에 대하여도 감사를 표하자.

> [!re] 개발 중단 2024-09-20
>
> 아래 레퍼런스 바탕으로 한번 시도했는데, `Google Sheet Api` 따위가 필요없었다.
> 다만,
>
>   1. 문서 공유 설정을 누구에게나 열어줘야하니 보안 요구조건에 따라 채택 불가능한 방법이 될 수 있다.
>   2. '쓰기'가 불가능한 작업이다.
>
> 그리하여 현재 개발 내용은 따로 브랜치를 파서 보관하고, Google Sheet Api를 사용하는 방법 다시 시도한다.


## 제작 과정: 2차 시도 [-]

1. 구글 API 활성화  클라우드 프로젝트 생성

   > [!rf]
   >
   > [video](https://www.youtube.com/watch?v=PFJNJQCU_lo)

   - 새 프로젝트 생성 (프로젝트명 compliment collector)
   - api 활성화: Google Sheets API
   - 사용자 인증 정보 생성:
     a. 애플리케이션 데이터를 선택하여 서비스 계정 생성
     b. 서비스 게정 이름 생성하여 robot email 확보
     _(이제 해당하는 API 대시보드의 사용자 인증 정보 탭에서 관련 내용을 확인할 수 있다.)_
   - 생성한 인증정보를 활용할 수 있는 key 생성:
     생성된 서비스계정을 클릭하고, `키` 탭을 클릭하여 `키 추가`
     - 비공개 키를 json형식으로 다운로드
   - 편집 하려는 google sheet에 공유 설정
     - 구글 시트 공유설정을 통해 서비스계정의 robot email을 대상으로 편집권한을 부여한다.

2. Google Sheet API에 접근하는 라이브러리

   - API 접근용 인증정보 관리

     > [!qt] OAuth를 사용하여 해볼까?
     >   󱞪 사용자 입장에서 너무 번거롭다. 우리 클라이언트는 구글 등 플랫폼에 로그인 하는 것을 매번 수동으로 하신다.

     [**AWS Lambda**를 사용하여 인증정보를 관리하고, 클라이언트는 이에 인증 결과만 받아서 사용하자.](AWS_Lambda를_사용하여_인증정보를_관리하고,_클라이언트는_이에_인증_결과만_받아서_사용하자.) [-]

   - 실패 사례
     - library: [google-auth-library](https://github.com/googleapis/google-auth-library-nodejs)
       - 이것은 nodejs 전용이라 브라우저에서는 사용할 수 없었다.
       - 관련 에러
       ```txt
       Module "buffer" has been externalized for browser compatibility. Cannot access "buffer.Buffer" in client code.
       See https://vitejs.dev/guide/troubleshooting.html#module-externalized-for-browser-compatibility for more details.
       ```


3. 시트 읽기, 쓰기: Google Sheets API 활용

   - library: [google-spreadsheet](https://github.com/theoephraim/node-google-spreadsheet)

4. UI 디자인
[-] 상의하고 해보자


## 배포 과정

github pages로 배포할거다.

1. basename 설정 _(vite니까)_

   ```typescript
   // vite.config.ts
   export default defineConfig({
     plugins: [react()],
     base: '/<repository-name>/', // 리포지토리 이름으로 변경
   })
   ```

2. gh-pages 설치 및 script 작성

   ```json
   // package.json
   {
     "scripts": {
       "build": "vite build",
       "predeploy": "npm run build",
       "deploy": "gh-pages -d dist"
     },
   }
   ```

   > [!re] private 저장소는 github-pages 활용 불가다.
