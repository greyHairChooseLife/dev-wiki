# 󰏢 Google Sheets를 데이터베이스로 활용한 React + TypeScript 프로젝트 가이드

## 1. Google Sheets API 설정
1. [Google Cloud Console](https://console.cloud.google.com/)에 접속하여 프로젝트 생성
2. **Google Sheets API**를 활성화
3. 서비스 계정을 만들고 JSON 형태로 인증 키를 다운로드
4. 구글 시트에서 서비스 계정 이메일을 공유하여 API에서 데이터를 읽고 쓸 수 있도록 권한 설정


## 2. React + Vite 프로젝트 시작

### Vite로 React 프로젝트 초기화:
```bash
npm create vite@latest
```

1. 프로젝트 생성 시 템플릿으로 **React + TypeScript** 선택
2. 프로젝트 디렉토리로 이동 후 의존성 패키지 설치:
```bash
cd your-project-name
npm install
```

### TypeScript 설정:
1. React와 TypeScript를 함께 사용하기 위한 타입 패키지 설치:
```bash
npm install @types/react @types/react-dom typescript
```

### Google Sheets API 관련 라이브러리 설치:
1. Google API를 사용하기 위한 `googleapis` 라이브러리 설치:
```bash
npm install googleapis
```


## 3. Google Sheets API 연동 코드 작성

Google Sheets API를 사용하여 데이터를 읽고 쓰는 코드를 작성합니다.

### Google Sheets API를 사용한 데이터 읽기 및 쓰기 예시 코드:
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


## 4. GitHub Pages로 배포

### `vite.config.js` 파일에서 `base` 경로 설정:
GitHub Pages는 정적 파일을 서빙하므로 배포 시 베이스 경로를 설정해야 합니다.
```javascript
export default {
  base: '/your-repo-name/',
};
```

### 프로젝트를 GitHub Pages로 배포:
```bash
npm run build
git add dist -f
git commit -m "Deploy"
git subtree push --prefix dist origin gh-pages
```

## 5. 중요 고려 사항

- Google Sheets API는 비동기로 작동하므로 적절한 로딩 상태 관리가 필요합니다.
- API 요청 시 보안 문제를 고려하여, API 키는 서버에서 관리하는 것이 좋습니다.
