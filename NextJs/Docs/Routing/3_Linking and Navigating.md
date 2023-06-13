# Linking and Navigating

Next.js 라우터는 아래에서 살펴볼 **클라이언트 사이드 네비게이션**과 함께 **서버 중심 라우터**를 사용한다. 이것은 **instant loading states** 그리고 **concurrent rendering**을 지원한다. 이는 클라이언트 사이드 상태를 유지하면서 탐색하고 값비싼 리렌더링을 피하고 방해를 받지 않으며 레이스 상태를 유발하지 않는다는 것을 의미한다.  

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

**dynamic segments**에 연결할 때, 너는 template literals를 이용해서 링크 리스트를 생성할 수 있다.

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
- 소프트 탐색조건이 충족된다면, 라우터는 서버가 아닌 캐시로부터 새 부분(세그먼트)를 가져옵니다. 그렇지 않은 경우, 라우터는 하드 탐색을 수행하고, 서버 구성요소 페이로드(전송데이터)를 가져옵니다.
  - 소프트 탐색(Soft Navigation) : 탐색 시 변경된 부분(세그먼트) 대한 캐시가 있는 경우 재사용되며, 새로운 데이터 요청이 되지 않는다.
  - 하드 탐색(Hard Navigation) : 탐색 중 캐시는 무효화되고, 변경된 부분(세그먼트)에 대해 서버 refetch와 rerender를 수행한다.

- 생성되는 경우, 서버로부터 페이로드를 fetch하는 동안 loading UI가 보인다.
- 라우터는 캐시와 새로운 페이로드를 클라이언트에서 렌더하는데에 사용한다.

### client-side Caching of Rendered Server Components (렌더링된 서버 컴포넌트의 클라이언트 측 캐싱)

> **Good To Know**  
>
>  client-side 캐시는 [서버 사이드의 Next.js HTTP cache](https://nextjs.org/docs/app/building-your-application/data-fetching#caching-data)와 다르다. 아래는 Next.js cache를 참고했다.
>
> - Next.js 캐시는 전체적으로 배포할 수 있는 영구 HTTP 캐시다. 즉, 플랫폼에 따라 캐시를 자동으로 확장하고 여러 지역에(ex. Vercel) 공유할 수 있다.
> - Next.js는 `fetch()`의 옵션 객체를 확장함으로서 각 요청이 영구 캐싱 동작을 설정할 수 있도록 한다. [컴포넌트 수준에서 데이터 fetch 기능](https://nextjs.org/docs/app/building-your-application/data-fetching#fetching-data-at-the-component-level)을 사용하면 데이터가 사용되는 곳에서 직접 캐싱을 구성할 수 있다.
> - 서버 렌더링 중에 Next.js가 데이터 fetch를 발견하면 캐시를 확인해서 이미 있는 데이터를 사용 가능한지 확인한다. 가능하면 캐시된 데이터를 반환한다. 그렇지 않으면, 나중에 데이터를 요청할 때 가져와서 저장한다.
> - 만약, `fetch`를 사용할 수 없는 경우 React는 요청 기간 동안 데이터를 수동으로 캐시할 수 있는 캐시 기능을 제공한다.

###### 새 라우터에는 서버 컴포넌트(페이로드)의 렌더링된 결과를 저장하는 인메모리 클라이언트 측 캐시가 있다. 캐시는 모든 수준에서 무효화를 허용하고 동시 렌더링 간 일관성을 보장하는 경로 세그먼트별로 캐시가 분할된다. 사용자가 앱을 탐색할 때 라우터는 이전에 fetch했던 segments와 prefetch했던 세그먼트를 캐시에 저장한다.  

즉, 특정상황에서 라우터는 새로운 요청을 하는 대신에 캐시를 가져올 수 있다. 이러면 불필요하게 데이터를 다시 불러오고 컴포넌트를 리렌더링하는 상황을 방지하여 성능을 향상시킬 수 있다.

### Invalidating the Cache (캐시 무효화)

서버 Action을 사용해서 요청 시 경로(revalidatePath) 또는 캐시 태그별로(revalidateTag) 데이터를 재검증할 수 있다.

### Prefetching

Prefetching은 방문하기 전에 background에서 경로를 미리 load하는 방법이다. prefetch된 경로의 렌더링결과는 라우터의 client-side측 캐시에 추가된다. 따라서, 사전 추출된 경로로 거의 즉시 이동할 수 있다.  

기본적으로, 라우터는 `<Link>` 컴포넌트를 사용하여 뷰포트에 표시되려고 할 때 미리 prefetch한다. 페이지를 처음 로드하거나, 스크롤할 때 발생할 수 있다. 라우트는 `useRouter()`훅의 `prefetch` 메소드를 사용해서 프로그래밍 방식으로 prefetch할 수 있다.

#### Static and Dynamic Routes

- 만약, 라우트가 정적이라면 경로에 대한 모든 서버 컴포넌트 페이로드는 prefetch될 것이다.
- 만약, 라우트가 동적이라면 첫번째 공유된 레이아웃부터 첫번째 loading.js파일까지 prefetch된다. 이는 전체 경로를 동적으로 prefetch하는 비용을 줄이고 동적 경로에 대한 로딩 상태를 허용한다.

> **Good To know**  
>
> - prefetching은 오직 production에서만 사용 가능하다.
> - prefetching을 disabled하려면 `<Link>`에서 `prefetch={false}`하면 된다.

### Soft Navigation

탐색 중 변경된 세그먼트에 대한 캐시는 재사용되며(만약, 있다면), 서버에 새로운 데이터 요청은 수행되지 않는다.

#### Soft Navigation에 대한 조건

탐색 중, Next.js는 prefetch를 했거나, 동적 세그먼트가 포함되어 있지 않거나 동적 매개변수가 있는 경우 소프트 탐색을 사용한다.  

예를 들어, 동적 `[team]` 세그먼트를 포함하는 다음 경로를 생각해보자: `/dashboard/[team]/*`.  

`/dashboard/[team]/*` 아래의 캐시된 세그먼트는 `[team]`parameter가 변경된 경우에는 유효하지 않다.

- `/dashboard/team-red/*`에서 `/dashboard/team-red/*`로 이동한 경우 soft navigation 조건에 만족한다.
- `/dashboard/team-red/*`에서 `/dashboard/team-blue/*`는 hard navigation이 될 것이다.

### Hard Navigation

탐색 중, **캐시가 무효화**되고 **데이터를 다시 가져와서** 변경된 세그먼트를 **다시 렌더링한다.**

### Back/Forward Navigation

뒤로 및 앞으로 탐색 시(popstate event)에는 soft navigation 동작을 갖는다. 이는 클라이언트 측 캐시가 재사용되고 탐색이 거의 즉시 수행된다는 것을 의미한다.



