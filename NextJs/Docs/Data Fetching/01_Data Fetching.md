# Data Fetching

Next.js App Router를 사용하면 함수를 비동기로 표시하고 Promise처리를 위해 async와 await를 사용함으로서 직접 데이터를 가져올 수 있다.  

데이터 fetching은 `fetch()` Web API와 리액트 서버컴포넌트 위에서 구축된다. `fetch()`를 사용할 때 요청은 자동으로 중복 제거 된다.  

Next.js는 각 요청이 자체 [캐싱 및 재검증](https://nextjs.org/docs/app/building-your-application/data-fetching/caching)을 할 수 있도록 `fetch`옵션 객체를 제공한다.

---

## 서버컴포넌트에서 `async` 와 `await`

서버 컴포넌트에서 data를 fetch하기 위해 `async`와 `await`를 사용할 수 있다.

`app/page.tsx`

```tsx
async function getData() {
  const res = await fetch('https://api...');
  
  if(!res.ok) {
    thorw new Error('Failed to fetch data');
  }
  
  return res.json()l
}

export default function Page() {
  const data = await getData();
  
  return ...
}
```

> **Good To Know**  
>
> 서버 컴포넌트에서 타입스크립트와 `async`를 사용하려면 Typescript 5.1.3 또는 @types/react 18.2.8 이상을 사용해야 한다.

### 서버 컴포넌트 기능

Next.js는 서버 컴포넌트에서 데이터를 fetching할 때 필요한 유용한 서버 기능을 제공한다.

- [cookies()](https://nextjs.org/docs/app/api-reference/functions/cookies)
- [headers()](https://nextjs.org/docs/app/api-reference/functions/headers)

---

## `use` in Client Components

`use`는 `await`와 개념적으로 유사한 promise를 받아들이는 새로운 리액트 기능이다. `use`는 함수가 반환한 promise를 컴포넌트, hooks, Suspense와 호환되는 방식으로 처리한다.  

`use`안에서 `fetch`를 래핑하는 것은 현재 클라이언트 컴포넌트에서 권장되지 않는다. 여러 리런데링을 트리거 할 수 있기 때문이다. 현재로서는 클라이언트 컴포넌트에서 데이터를 가져와야 하는 경우 SWR이나 React Query같은 third-party 라이브러리를 이용하는 것이 좋다.

---

## Static Data Fetching

기본적으로 `fetch`는 자동으로 데이터를 가져온 뒤 무기한으로 캐시한다.

```
fetch('https://...') // cache: 'force-cache' is the default
```

### Revalidating Data

지정된 시간 간격으로 데이터를 다시 확인하려면 `fetch()`에서 `next.revalidate`옵션을 사용하여 리소스의 수명을 설정할 수 있다.

```
fetch('https://...', { next: { revalidate: 10 } })
```

[Revalidating Data](https://nextjs.org/docs/app/building-your-application/data-fetching/revalidating)에서 더 자세한 정보를 확인할 수 있다.

> **Good To Know**  
>
> `revalidate`또는 `cache: 'force-cache'`를 사용해서 fetch level에서 공유된 데이터를 저장한다. 이 때 사용자별 데이터는 피해야 한다.(cookies(), headers())

---

## Dynamic Data Fetching

`fetch`요청 때마다 새로운 데이터를 받아오려면 `cache: 'no-store'`옵션을 사용한다.

```
fetch('https://...', { cache: 'no-store' })
```

---

## Data Fetching Patterns

### 병렬 Data Fetching

클라이언트-서버 waterfall를 최소화 하기 위해 데이터를 병렬로 가져오는 패턴을 사용하는 것이 좋다.

> 여기서 waterfall은 단계적으로 완료되면 진행하고 이런 식을 뜻한다.

`app/artist/[username]/page.tsx`

```jsx
async function getTodos(num) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${num}`);
  return res.json();
}

async function anotherGetTodos(num) {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${num + 1}`
  );
  return res.json();
}

export default async function Page({ params: { num } }) {
  // Promise가 resolve될 때까지 기다린다. Promise.all을 사용하면 각 promise들을 비동기로 실행하고 마지막 Promise가 끝나면 반환한다.
  const [first, second] = await Promise.all([
    getTodos(num),
    anotherGetTodos(num),
  ]);

  return (
    <>
      num
      <p>firstTodoId: {first.id}</p>
      <p>secondTodoId: {second.id}</p>
    </>
  );
}

```

위와 같이 Promise.all로 병렬적으로 fetch를 할 경우, 서버 컴포넌트에서 `await`를 호출하기 전에 각 fetch요청을 동시에 시작할 수 있다. 하지만, 두 Promise들이 resolve될 때까지 렌더링 된 결과를 볼 수 없다. 사용자 경험을 개선하기위해 suspense 영역을 추가할 수 있다.

`app/artist/[username]/page.tsx`

```tsx
import { getArtist, getArtistAlbums, type Album } from './api';

export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  // Initiate both requests in parallel
  const artistData = getArtist(username)
  const albumData = getArtistAlbums(username)
 
  // Wait for the artist's promise to resolve first
  const artist = await artistData
 
  return (
    <>
      <h1>{artist.name}</h1>
      {/* Send the artist information first,
          and wrap albums in a suspense boundary */}
      <Suspense fallback={<div>Loading...</div>}>
        <Albums promise={albumData} />
      </Suspense>
    </>
  )
}

// Albums Component
async function Albums({ promise }: { promise: Promise<Album[]> }) {
  // Wait for the albums promise to resolve
  const albums = await promise
 
  return (
    <ul>
      {albums.map((album) => (
        <li key={album.id}>{album.name}</li>
      ))}
    </ul>
  )
}
```

이렇게 하면 각 fetch요청이 병렬적으로 이루어지고, albumData를 Promise형태로 내리면서 Suspense를 통해 fallback이 동작한다.  

컴포넌트 구조 개선을 위해 [preloading pattern](https://nextjs.org/docs/app/building-your-application/data-fetching/caching#preload-pattern-with-cache)을 참고할 수 있다. 해당 페이지로 가면 미리 fetch하고 cash된 데이터를 사용하게 한다.

### 순차적 데이터 가져오기

데이터를 순차적으로 가져오기 위해 컴포넌트 내부에서 fetch한 결과 데이터를 하위 컴포넌트로 보내서 하위 컴포넌트에서 또 fetch를 할 수 있다.

```tsx
// ...
 
async function Playlists({ artistID }: { artistID: string }) {
  // Wait for the playlists
  const playlists = await getArtistPlaylists(artistID)
 
  return (
    <ul>
      {playlists.map((playlist) => (
        <li key={playlist.id}>{playlist.name}</li>
      ))}
    </ul>
  )
}
 
export default async function Page({
  params: { username },
}: {
  params: { username: string }
}) {
  // Wait for the artist
  const artist = await getArtist(username)
 
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <Playlists artistID={artist.id} />
      </Suspense>
    </>
  )
}
```

컴포넌트 안에서 하는 fetch나 더 중첩되어 있는 하위 세그먼트는 이전 요청 혹은 세그먼트가 완료될 때까지 data fetching을 시작 할 수 없다.

### Blocking Rendering in a Route

layout에서 데이터를 fetch하면, 그 아래에 있는 모든 경로 세그먼트에 대한 렌더링은 로딩이 완료된 후에 시작할 수 있다.

`app`디렉토리의 경우 추가적인 선택지가 있다.

1. 첫 번째, `loading.js`를 사용해서 데이터 fetching 기능의 결과로 스트리밍하는 동안 즉시 로딩을 표시할 수 있는 방법.

   > 스트리밍이란?  
   >
   > React, Next.js에 있는 스트리밍을 사용하게 되면 페이지의 HTML을 더 작은 청크로 분할해서 클라이언트로 점진적으로 전송한다. 이렇게 하면 UI를 렌더링하기 전에 모든 데이터를 로드할 때까지 기다리지 않고 페이지 일부를 더 빠르게 표시한다. 각 컴포넌트가 청크로 간주될 수 있기 때문에 React의 컴포넌트와 잘 어울린다. 우선순위가 높거나 데이터에 의존하지 않는 컴포넌트는 먼저 전송할 수 있고, 리액트는 Hydrate를 더 일찍 시작할 수 있다. 우선순위가 낮은 컴포넌트는 데이터만 가져온 후에 서버에 요청할 수 있다. 스트리밍은 TTFB(Time to First Byte) 및 FCP(First Contentful Paint)를 줄일 수 있다. 특히 느린 장치에서 TTI를 개선하는데 도움이 된다.

2. 두 번째, 컴포넌트 트리에서 data fetching을 하위 컴포넌트로 이동해서 필요한 페이지 부분만 렌더링을 차단하는 방법. 예를 들어, 루트 레이아웃에서 data fetching을 하는 것이 아니라 특정 컴포넌트로 이동시키는 것이다. 가능하면 데이터를 사용하는 세그먼트에서 fetch하는 것이 좋다. 이렇게 하면 전체 페이지를 로딩하지 않고 필요한 페이지만 로딩할 수 있다.

---

## Default Caching Behavior

`fetch`를 사용하지 않는 data fetching 라이브러리들은 route 캐싱에 영향을 주지 않으며, route segment에 따라서 static하거나 dynamic하게 동작할 것이다.  

기본값인 segment가 정적인 경우에는 요청의 결과가 캐시되고 세그먼트의 나머지 부분과 함께 유효성 재검증된다.  

segment가 동적인 경우에는 요청의 결과가 캐시되지 않으며 세그먼트가 렌더링될 때마다 모든 요청을 다시 가져온다.

> **Good To Know**  
>
> `cookies()`나 `headers()`같은 동적 함수는 route segment를 동적으로 만든다.

---

## Segment Cache Configuration

임시 솔루션으로 타사 쿼리의 캐싱 동작을 구성할 수 있을 때까지 segment 설정을 사용하여 전체 segment의 캐시 동작을 사용자 정의할 수 있다. 자세한 내용은 [segment configuration](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#revalidate)을 참고하자.

`app/page.tsx `

```tsx
import prisma from './lib/prisma'
 
export const revalidate = 3600 // revalidate every hour
 
async function getPosts() {
  const posts = await prisma.post.findMany()
  return posts
}
 
export default async function Page() {
  const posts = await getPosts()
  // ...
}
```



> Data Fetching에 추가적으로 Cashing, Revalidating, Server Actions가 있다.
