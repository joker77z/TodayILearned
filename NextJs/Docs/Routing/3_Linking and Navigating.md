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

### Linking to Dynamic Segments(동적 세그먼트에 연결)

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

### Checking Active Links (활성 링크 확인)

`usePathname()`을 사용하여 링크가 활성 상태인지 확인할 수 있다. 예를 들어서, 활성 링크에 class를 추가하려면 현재 경로 이름이 링크의 href와 일치하는지 확인할 수 있다.

`app/ui/Navigation.jsx`

```jsx
'use client';

import { usePathname } from 'next/navigation';
import { Link } from 'next/link';

export function Navigation({ navLinks }) {
  const pathname = usePathname();
  
  return (
    <>
      {navLinks.map((link) => {
        const isActive = pathname.startsWith(link.href);
        
        return (
          <Link
            className={isActive ? 'text-blue' : 'text-black'}
            href={link.href}
            key={link.name}
          >
            {link.name}
          </Link>
        );
      })}
    </>
  )
}
```

### Scrolling to an `id` (id로 스크롤)

`<Link>`의 기본 동작은 변경된 경로 세그먼트의 맨 위로 스크롤하는 것이다. `href`안에 id가 있으면 일반 `<a>`태그와 유사하게 특정 id로 스크롤된다.  

맨 위로 스크롤 되는 것을 방지하려면 `scroll={false}`로 설정하고 id를 전달한다.

```jsx
<Link href="/#hashid" scroll={false}>
  text..
</Link>
```

---

## `useRouter()` Hook

`useRouter` hook을 사용하면 클라이언트 컴포넌트 내에서 경로를 프로그래밍 방식으로 변경할 수 있다.  

`useRouter`를 사용하기 위해, 클라이언트 컴포넌트 내에서  `next/navigation`으로부터 import하고 hook을 call해야 한다.

`app/page.jsx`

```jsx
'use client';

import { useRouter } from 'next/navigation';

export default function Page() {
  const router = useRouter();
  
  return (
    <button type="button" onClick={() => router.push('/dashboard')}>
      Dashboard
    </button>
  );
}
```

`useRouter`는 `push()`, `refresh()` 그리고 더 많은 메소드를 제공한다. [API reference](https://nextjs.org/docs/app/api-reference/functions/use-router)를 참고하자.

> **권장사항**  
>
> `useRouter`를 사용해야 하는 특별한 요구사항이 없는 경우 `<Link>` 컴포넌트를 사용해서 탐색하는 것이 좋다.

---

## How Navigation Works (탐색 작동 방식)

- 경로 전환은 `<Link>` 사용하거나 또는 `router.push()`를 호출하면서 시작된다.
- 라우터는 브라우저의 주소 표시줄에 있는 URL을 업데이트 한다.
- 라우터는 [클라이언트 캐시](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#client-side-caching-of-rendered-server-components)에서 변경되지 않은 세그먼트(ex. 공유 레이아웃)을 재사용해서 불필요한 작업을 방지한다. 이를 부분 렌더링이라고 한다.
- 소프트 탐색조건이 충족된다면, 라우터는 서버가 아닌 캐시로부터 새 세그먼트를 가져옵니다.



