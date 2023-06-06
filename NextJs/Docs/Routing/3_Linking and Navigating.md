# Linking and Navigating

Next.js 라우터는 아래에서 살펴볼 **<u>클라이언트 사이드 네비게이션</u>**과 함께 **<u>서버 중심 라우터</u>**를 사용한다. 이것은 **<u>instant loading states</u>** 그리고 **<u>concurrent rendering</u>**을 지원한다. 이는 클라이언트 사이드 상태를 유지하면서 탐색하고 값비싼 리렌더링을 피하고 방해를 받지 않으며 레이스 상태를 유발하지 않는다는 것을 의미한다.  

라우트 간 탐색하기 위한 두 가지 방법이 있다.

- `<Link>` Component
- `useRouter` Hook

이 페이지에서는 `<Link>`, `useRouter()` 그리고 네비게이션 동작 방식에 대해 설명한다.

---

## <Link> Component

`Link`는 HTML `<a>`요소를 확장하여 경로간 prefetching 및 클라이언트 측 탐색을 제공하는 React 컴포넌트다. 이것은 **Next.js에서 경로간 탐색하기 위해 일반적인 방법이다.**

`<Link>`컴포넌트를 사용하기 위해서 `next/link`를 import하고 `href` prop을 컴포넌트로 전달한다.

`app/page.tsx`

```tsx
import Link from 'next/link';

export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>;
}
```

`<Link>`로 전달할 수 있는 선택적인 props가 있다. [API reference](https://nextjs.org/docs/app/api-reference/components/link)를 참고할 수 있다.

---

##  Examples

### Linking to Dynamic Segments

**<u>dynamic segments</u>**에 연결할 때, 너는 template literals를 이용해서 링크 리스트를 생성할 수 있다.

`app/blog/PostList.jsx`

```jsx
import Link from 'next/link';

export default function PostList({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```



