# Server Component vs Client Component



Next.js 13+ (App Router)에서는 모든 컴포넌트가 기본적으로 **Server Component**입니다. 클라이언트 측 상호작용(클릭, 입력 등)이 필요할 때만 **Client Component**로 명시하여 사용합니다.


## 1. 주요 차이점

| 특징            | Server Component (기본)                            | Client Component                |
|-----------------|----------------------------------------------------|---------------------------------|
| **렌더링 위치** | 서버에서 렌더링 (HTML 생성 후 전송)                | 클라이언트(브라우저)에서 렌더링 |
| **데이터 페칭** | DB/API 직접 접근 가능 (async/await)                | 클라이언트에서 API 요청 필요    |
| **상호작용**    | 불가능 (`onClick`, `onChange` 등)                  | 가능 (이벤트 리스너 사용)       |
| **Hooks 사용**  | 불가능 (`useState`, `useEffect` 등)                | 가능                            |
| **번들 사이즈** | 클라이언트로 JS 코드가 전송되지 않음 (Zero Bundle) | 클라이언트로 JS 코드가 전송됨   |



### 언제 무엇을 써야 할까?

*   **Server Component 사용**:
    *   데이터를 가져올 때 (Data Fetching)
    *   백엔드 리소스(DB 등)에 직접 접근할 때
    *   민감한 정보(API Key 등)를 서버에 숨겨야 할 때
    *   페이지의 초기 로딩 속도를 높이고 싶을 때 (HTML 미리 생성)


*   **Client Component 사용**:
    *   `onClick`, `onChange` 등 이벤트 리스너가 필요할 때
    *   `useState`, `useEffect` 등 React Hooks를 써야 할 때
    *   브라우저 전용 API(`window`, `localStorage` 등)를 사용할 때


-   **e.g.**

    - Use a Server Component to fetch and render the post content statically.
    - Wrap interactive elements (e.g., edit/delete buttons) in a Client Component.

    ````javascript
    // filepath: app/blog/[slug]/page.js (Server Component for data)
    import { fetchPost } from '@/lib/api';

    export default async function BlogPost({ params }) {
      const post = await fetchPost(params.slug);
      return (
        <div>
          <h1>{post.title}</h1>
          <p>{post.content}</p>
          <PostActions postId={post.id} /> {/* Client Component for interactions */}
        </div>
      );
    }

    // filepath: components/PostActions.js (Client Component)
    'use client';

    import { useRouter } from 'next/navigation';

    export default function PostActions({ postId }) {
      const router = useRouter();

      const handleEdit = () => router.push(`/edit/${postId}`);
      const handleDelete = () => {
        // Delete logic, e.g., API call
        router.push('/');
      };

      return (
        <div>
          <button onClick={handleEdit}>Edit</button>
          <button onClick={handleDelete}>Delete</button>
        </div>
      );
    }
    ````




## 2. 사용 가능한 API 비교

서버 컴포넌트와 클라이언트 컴포넌트는 실행 환경이 다르기 때문에 사용할 수 있는 API가 명확히 구분됩니다.

| 구분              | Server Component                        | Client Component                                                     |
| :---              | :---                                    | :---                                                                 |
| **React Hooks**   | 사용 불가                               | ✅ 사용 가능                                                         |
| **Next.js Hooks** | 사용 불가                               | ✅ 사용 가능                                                         |
| **Data Fetching** | ✅ `async/await`, `fetch` 직접 사용     | ❌ `useEffect` 내부에서 호출하거나 React Query 등 라이브러리 필요    |
| **Cookie/Header** | ✅ `cookies()`, `headers()` (읽기 전용) | ❌ 브라우저 API (`document.cookie`) 사용                             |
| **Redirect**      | ✅ `redirect()`, `permanentRedirect()`  | ✅ `useRouter().push()`                                              |

### 주요 API 상세


#### 1. Client Only (Hooks)

React의 상태 관리 및 생명주기 관련 Hooks는 브라우저의 메모리와 렌더링 과정에 의존하므로 **Client Component**에서만 동작합니다.

*   `useState`, `useEffect`, `useCallback`, `useMemo`, `useReducer`, `useContext` 등
*   `useRouter`, `usePathname`, `useSearchParams` **(Next.js 라우팅 훅)**


#### 2. Server Only (Server-side Functions)

서버에서만 접근 가능한 리소스나 요청 정보를 다루는 함수들은 **Server Component**에서만 사용 가능합니다.

*   `cookies()`, `headers()`: 요청의 쿠키와 헤더 정보 읽기
*   `redirect()`: 서버 사이드 리다이렉트 (렌더링 중단 및 이동)


#### 3. 공통 사용 가능

일부 유틸리티나 헬퍼 함수는 양쪽 모두에서 사용할 수 있지만, 용도가 다를 수 있습니다.

*   `Link` 컴포넌트 (`next/link`)
*   `Image` 컴포넌트 (`next/image`)



## 3. 코드 예시

### Server Component

데이터를 서버에서 직접 가져와서 렌더링합니다. `async/await`를 컴포넌트 레벨에서 바로 사용할 수 있습니다.

```tsx
async function getData() {
  // DB나 외부 API 직접 호출
  const res = await fetch('https://api.example.com/posts/1');
  return res.json();
}

export default async function ServerPage() {
  const data = await getData();

  return (
    <div>
      <h1>서버 컴포넌트</h1>
      {/* 데이터가 이미 채워진 상태로 HTML이 전송됨 */}
      <p>Title: {data.title}</p>
    </div>
  );
}
```


### Client Component

파일 최상단에 `'use client'`를 선언해야 합니다. 상태(`useState`)나 이벤트(`onClick`)를 사용할 수 있습니다.

```tsx
'use client'; // 필수 선언

import { useState } from 'react';

export default function ClientCounter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>클라이언트 컴포넌트</h1>
      <p>Count: {count}</p>
      {/* 브라우저 상호작용 가능 */}
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  );
}
```



