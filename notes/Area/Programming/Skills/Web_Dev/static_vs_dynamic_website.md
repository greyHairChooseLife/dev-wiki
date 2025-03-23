# 󰏢 static vs dynamic website




## 기본 개념 차이

### 정적(Static) 웹사이트

- 미리 작성된 HTML/CSS/JS 파일을 서버에 저장해두고 그대로 제공
- 모든 사용자에게 동일한 콘텐츠 제공
- 서버 측 처리나 데이터베이스 연결 없음
- 클라이언트에서 JavaScript를 통해 API 호출은 가능함
- 빠른 로딩 속도와 높은 보안성
- 예: GitHub Pages, Netlify, Vercel 등의 정적 호스팅 서비스


### 동적(Dynamic) 웹사이트

- 서버에서 요청 시점에 콘텐츠를 생성하여 제공
- 사용자, 시간, 위치 등에 따라 다른 콘텐츠 제공 가능
- 서버 측 언어(PHP, Python, Node.js 등)와 데이터베이스 활용
- 사용자 인증, 개인화된 경험, 데이터 저장/처리 등 가능
- 예: 쇼핑몰, SNS, 뱅킹 애플리케이션 등


### CSR vs SSR과의 차이점

- CSR(Client-Side Rendering)과 SSR(Server-Side Rendering)은 렌더링 방식을 나타냄
- 정적/동적 구분은 콘텐츠 생성 방식과 서버의 역할에 관한 것
- 동적 웹사이트가 항상 SSR을 사용하는 것은 아님 (SPA도 동적 웹사이트일 수 있음)
- 정적 웹사이트도 CSR을 통해 동적인 UI 경험 제공 가능

_즉, 정적/동적의 핵심 차이는 서버에서 요청 시점에 콘텐츠를 생성하고 데이터를 처리하는 능력의 유무_


## 아키텍쳐

### 정적 웹사이트

html, css, js파일만 서버에서 제공하면 된다. 즉, 소스코드를 빌드해서 나온 결과물을 nginx같은 가벼운
웹서버를 통해 제공하면 된다.

```dockerfile
FROM nginx:alpine
COPY ./build /usr/share/nginx/html
EXPOSE 80
```

- github action을 활용한 빌드, 배포 자동화
- 무중단 배포
- DB 연동

### 동적 웹사이트

- front-end
- back-end
- database
