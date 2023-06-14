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