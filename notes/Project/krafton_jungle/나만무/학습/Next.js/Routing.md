# Next.js App Router: Layout & Components

Next.js의 App Router에서 **Layout**은 페이지가 변경되어도 유지되는 UI 껍데기입니다. 공통 컴포넌트를 효율적으로 관리하는 방법을 보여줍니다.


## 1. 디렉토리 구조 (확장됨)

공통 컴포넌트(`components/`)와 하위 페이지(`settings/`)가 추가된 구조입니다.

```text
app/
├── components/
│   └── Sidebar.tsx     (공통 컴포넌트: 별도 파일로 분리)
├── layout.tsx          (Root Layout)
├── page.tsx            (Home Page)
└── dashboard/
    ├── layout.tsx      (Dashboard Layout: Sidebar를 여기서 사용)
    ├── page.tsx        (Dashboard Main: /dashboard)
    └── settings/
        └── page.tsx    (Dashboard Settings: /dashboard/settings)
```


## 2. 공통 컴포넌트 분리

여러 곳에서 쓰이거나 코드가 긴 네비게이션은 별도 컴포넌트로 분리합니다.

```tsx
// app/components/Sidebar.tsx
export default function Sidebar() {
  return (
    <nav>
      <ul>
        <li><a href="/dashboard">홈</a></li>
        <li><a href="/dashboard/settings">설정</a></li>
      </ul>
    </nav>
  )
}

```


## 3. 레이아웃에서 공통 컴포넌트 사용

`DashboardLayout`에서 `Sidebar`를 **한 번만 임포트**합니다.
이렇게 하면 `/dashboard` 하위의 모든 페이지는 자동으로 사이드바를 갖게 됩니다.

```tsx
// app/dashboard/layout.tsx
import Sidebar from '../components/Sidebar';

export default function DashboardLayout({ children, }: { children: React.ReactNode }) {
  return (
    <section>
      {/* 사이드바 (고정) */}
      <Sidebar />
      
      {/* 페이지 콘텐츠 (가변) */}
      <div>
        {children}
      </div>
    </section>
  )
}

```


## 4. 여러 자식 페이지 (Multiple Children)

`layout.tsx`는 그대로 있고, URL에 따라 `children`으로 전달되는 컴포넌트만 바뀝니다.

### A. 대시보드 메인 (`/dashboard`)

```tsx
// app/dashboard/page.tsx
export default function DashboardPage() {
  return <h1>대시보드 메인 화면입니다.</h1>
}
```

### B. 대시보드 설정 (`/dashboard/settings`)

```tsx
// app/dashboard/settings/page.tsx
export default function SettingsPage() {
  return <h1>환경 설정 페이지입니다.</h1>
}
```


## 5. 최종 렌더링 구조 (HTML)

브라우저에서 `/dashboard/settings`로 접속했을 때의 최종 HTML 구조입니다.
`DashboardLayout`이 리턴했던 `<section>` 태그가 자식들을 감싸고 있는 것을 볼 수 있습니다.

```tsx
<RootLayout>
  <html>
    <body>
      {/* ... Root Header ... */}
      
      {/* DashboardLayout 시작 */}
      <section>
        <Sidebar />
        <div>
          {/* URL에 맞는 자식 페이지 (SettingsPage) */}
          <SettingsPage />
        </div>
      </section>
      {/* DashboardLayout 끝 */}
      
      {/* ... Root Footer ... */}
    </body>
  </html>
</RootLayout>

```

