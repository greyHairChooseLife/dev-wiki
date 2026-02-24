# Next.js App Router: Dynamic Routing



Next.js에서 **Dynamic Routing**은 URL의 특정 부분을 변수처럼 사용하여, 동일한 레이아웃과 로직으로 여러 페이지를 생성하는 기능입니다. 폴더 이름을 대괄호(`[]`)로 감싸서 정의합니다.

## 1. 디렉토리

(개별 게시글: /blog/1, /blog/abc)


## 2. 동적 페이지 구현 (`[id]/page.tsx`)

동적 경로의 파라미터는 `params` prop을 통해 접근할 수 있습니다.
`params`는 Promise이므로 ` 구조

블로그 게시글처럼 ID에 따라 내용이 바뀌는 구조입니다.

```text
app/
├── blog/
│   ├── page.tsx        (블로그 목록: /blog)
│   └── [id]/           (동적 세그먼트)
│       └── page.tsx    await` 처리가 필요할 수 있습니다 (Next.js 버전에 따라 다름, 최신 버전 기준 비동기 접근 권장).
```

```tsx
// app/blog/[id]/page.tsx
interface PageProps {
  params: { id: string }
}

export default function BlogPost({ params }: PageProps) {
  // URL이 /blog/123 이라면 params.id는 "123"
  return (
    <div>
      <h1>게시글 ID: {params.id}</h1>
      <p>이곳에 {params.id}번 게시글의 내용이 들어갑니다.</p>
    </div>
  )
}
```


## 3. 정적 파라미터 생성 (`generateStaticParams`)

빌드 시점에 미리 페이지를 생성(SSG)하고 싶다면 `generateStaticParams`를 사용합니다.

```tsx
// app/blog/[id]/page.tsx

// 이 함수가 리턴하는 배열의 id 값만큼 정적 페이지가 생성됩니다.
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then((res) => res.json())
  
  return posts.map((post: any) => ({
    id: String(post.id),
  }))
}

export default function BlogPost({ params }: { params: { id: string } }) {
  return <h1>Post: {params.id}</h1>
}
```


## 4. Catch-all Segments (`[...slug]`)

URL의 깊이가 가변적일 때 사용합니다. (예: `/docs/a`, `/docs/a/b`, `/docs/a/b/c`)

*   `app/docs/[...slug]/page.tsx`
*   URL: `/docs/a/b` -> `params.slug`: `['a', 'b']`

```tsx
export default function DocsPage({ params }: { params: { slug: string[] } }) {
  return (
    <div>
      <h1>경로 깊이: {params.slug.length}</h1>
      <p>현재 경로: {params.slug.join(' > ')}</p>
    </div>
  )
}
```


---


Catch-all segments (`[...slug]`) in Next.js are useful for handling dynamic routes where the URL depth can vary, allowing you to capture multiple path segments as an array. This is practical for scenarios requiring flexible navigation or data fetching based on nested paths. Here are a few concrete, real-world examples:


### 1. Documentation Site with Nested Categories

Imagine a docs site where users can navigate deep into topics like `/docs/getting-started/installation` or `/docs/advanced/api-reference/endpoints`. You can use `[...slug]` to render pages dynamically without defining every possible route.

````tsx
// filepath: app/docs/[...slug]/page.tsx
export default function DocsPage({ params }: { params: { slug: string[] } }) {
  const { slug } = params;
  // Fetch content based on slug, e.g., from a CMS or file system
  const content = fetchDocContent(slug.join('/')); // e.g., 'getting-started/installation'

  return (
    <div>
      <h1>{content.title}</h1>
      <p>{content.body}</p>
      <nav>
        {slug.map((segment, index) => (
          <span key={index}>{segment} {index < slug.length - 1 ? ' > ' : ''}</span>
        ))}
      </nav>
    </div>
  );
}
````

This allows users to bookmark or share deep links, and you can generate breadcrumbs or fetch nested data easily.


### 2. Blog with Date-Based Archives

For a blog where posts are organized by year/month/day, like `/blog/2023/12/15/my-post`, `[...slug]` captures the date parts to query posts.

````tsx
// filepath: app/blog/[...slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string[] } }) {
  const [year, month, day, ...titleParts] = params.slug;
  const postSlug = titleParts.join('-');
  // Query database for post by date and slug
  const post = fetchPost({ year, month, day, slug: postSlug });

  return (
    <article>
      <h1>{post.title}</h1>
      <time>{year}-{month}-{day}</time>
      <p>{post.content}</p>
    </article>
  );
}
````

This is useful for SEO-friendly URLs and filtering posts by date hierarchies.
