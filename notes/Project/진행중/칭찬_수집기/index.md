# 󰏢 칭찬 수집기, compliment collector

> [!cn] 개념정리
>
> - 여자친구랑 칭찬에 관해 대화하다가 나온 아이디어.
> - 어린 자녀에게 칭찬을 할 때 **결과가 아니라 과정**을 칭찬하는 것이 좋겠다는 대화였다.
> - 그런데 우리부터가 잘 못하니, 서로 하루에 하나씩 칭찬하면서 이것을 분석해보자는 결론.
>
> [배포](https://greyhairchooselife.github.io/compliment-collector/)

## Update Log

[x] 하루 한번만 칭찬을 할 수 있도록 제한, 하루의 기준은 한국 기준 24:00
[x] 칭찬 기록에 시간 표현은 현지 기준 읽기 쉬운 형태로 수정(현재 UTC 기준)

> [!dt]2024-10-16
> [x] cors 설정을 통해 AWS lambda의 요청을 제한적으로 허용. 쉽게말해 배포한 gh-pages domain에서만 해당 lambda를 호출할 수 있도록 설정.
>
>  󱞪 _AWS API gateway에서 각 api마다 cors설정 할 수 있다._
>
>   - `Access-Control-Allow-Origin` 옵션에 접근 가능한 도메인을 지정해준다.
>   - `Access-Control-Allow-Methods` 옵션에 `GET, OPTIOPNS`를 지정해 준다.
>   - AWS lambda에서 return 헤더에 아래와같이 추가해준다.
>     ```javascript
>     return {
>       statusCode: 200,
>       headers: {
>         'Access-Control-Allow-Headers': '*',
>         'Access-Control-Allow-Origin': 'https://greyhairchooselife.github.io',
>         'Access-Control-Allow-Methods': 'GET, OPTIONS', // OPTIONS을 추가하지 않으면 CORS 오류 발생
>       },
>       body: JSON.stringify({
>        some: 'data',
>       }),
>     };
>     ```


## 개발 가이드라인 with chatGPT

- 요청 프롬프트:

  > 구글 sheets를 데이터베이스로 활용하는 간단한 스테틱 어플리케이션을 만들거야.
  > 배포는 github pages로 하고싶은데, vite, react, typescript로 프로젝트를 시작할거야.
  >
  > google sheets와 연결하고 데이터를 쓰고 읽는 api 사용이 관건이겠는데, 이 부분에 대해서는 경험이 전혀 없어.
  >
  > 한번 가이드해봐

### 1. Google Sheets API 설정

1. [Google Cloud Console](https://console.cloud.google.com/)에 접속하여 프로젝트 생성
2. **Google Sheets API**를 활성화
3. 서비스 계정을 만들고 JSON 형태로 인증 키를 다운로드
4. 구글 시트에서 서비스 계정 이메일을 공유하여 API에서 데이터를 읽고 쓸 수 있도록 권한 설정


### 2. Google Sheets API 연동 코드 작성

Google Sheets API를 사용하여 데이터를 읽고 쓰는 코드를 작성합니다.

#### Google Sheets API를 사용한 데이터 읽기 및 쓰기 예시 코드:
```typescript
import { google } from 'googleapis';

// 인증 정보 입력
const sheets = google.sheets({ version: 'v4', auth: "your-auth" });

// Google Sheets에서 데이터 읽기
async function getSheetData() {
  const res = await sheets.spreadsheets.values.get({
    spreadsheetId: 'your-spreadsheet-id',
    range: 'Sheet1!A1:D5', // 원하는 셀 범위
  });
  console.log(res.data.values);
}

// Google Sheets에 데이터 추가
async function appendSheetData(newData: string[][]) {
  const res = await sheets.spreadsheets.values.append({
    spreadsheetId: 'your-spreadsheet-id',
    range: 'Sheet1!A1',
    valueInputOption: 'RAW',
    requestBody: {
      values: newData,
    },
  });
  console.log('Data Appended:', res.data.updates);
}
```


### 3. GitHub Pages로 배포

#### `vite.config.js` 파일에서 `base` 경로 설정:
GitHub Pages는 정적 파일을 서빙하므로 배포 시 베이스 경로를 설정해야 합니다.
```javascript
export default {
  base: '/your-repo-name/',
};
```

#### 프로젝트를 GitHub Pages로 배포:
```bash
npm run build
git add dist -f
git commit -m "Deploy"
git subtree push --prefix dist origin gh-pages
```

### 4. 중요 고려 사항

- Google Sheets API는 비동기로 작동하므로 적절한 로딩 상태 관리가 필요합니다.
- API 요청 시 보안 문제를 고려하여, API 키는 서버에서 관리하는 것이 좋습니다.



## 제작 과정: 1차 시도

1. vite, ts, react 프로젝트 생성
2. 시트 읽기: Google Sheets API 없이

   - 시트별로 가져오기
   - SQL 문법으로 가져오기

   > [!rf]
   >
   > [video](https://www.youtube.com/watch?v=cRwpTv33Z_g)
   > [repo](https://github.com/theotrain/load-google-sheet-data-using-sql)

> [!re] 개발 중단 2024-09-20
>
> 아래 레퍼런스 바탕으로 한번 시도했는데, `Google Sheet Api` 따위가 필요없었다.
> 다만,
>
>   1. 문서 공유 설정을 누구에게나 열어줘야하니 보안 요구조건에 따라 채택 불가능한 방법이 될 수 있다.
>   2. '쓰기'가 불가능한 작업이다.
>
> 그리하여 현재 개발 내용은 따로 브랜치를 파서 보관하고, Google Sheet Api를 사용하는 방법 다시 시도한다.


## 제작 과정: 2차 시도

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

     [**AWS Lambda**를 사용하여 인증정보를 관리하고, 클라이언트는 이에 인증 결과만 받아서 사용하자.](AWS_Lambda를_사용하여_인증정보를_관리하고,_클라이언트는_이에_인증_결과만_받아서_사용하자.)

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

  내맘대루



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
