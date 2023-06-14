# Static and Dynamic Rendering

Next.js에서 route는 정적 혹은 동적으로 렌더링 될 수 있다.

- 정적 route에서 컴포넌트는 빌드 시 서버에서 렌더링된다. 작업 결과가 캐시되어 이후 요청 시 재사용된다.
- 동적 route에서 컴포넌트는 요청 할 때 서버에서 렌더링된다.

---

## Static Rendering (Default)

기본적으로, Next.js는 route 성능 향상을 위해 정적 렌더링한다. 이는 모든 렌더링 작업이 사전에 완료되고 사용자에게 지리적으로 더 가까운 CDN에서 서비스를 제공할 수 있음을 의미한다.

---

## Static Data Fetching (Default)

기본적으로, Next.js는 캐싱 행위를 의도적으로 그만두지 않는다면 `fetch()`요청의 결과를 캐시한다. 이 의미는 `cache`옵션을 설정하지 않으면 기본적으로 `force-cache`옵션을 사용하는 fetch 요청을 한다는 의미다.  

`revalidate`옵션을 사용하면 route는 재검증중에 정적으로 다시 리렌더링된다. 캐싱에 대한 추가 글은 [Caching and Revalidating](https://nextjs.org/docs/app/building-your-application/data-fetching/caching)페이지를 참고하자.

---

## Dynamic Rendering (클라이언트 렌더링)

정적 렌더링 중에 동적 기능 또는 동적 `fetch()`요청(no caching)이 발견될 경우, Next.js는 요청 시간동안 동적 렌더링으로 전환한다. 캐시된 데이터 요청은 동적 렌더링 중에도 재사용할 수 있다.  

다음 표는 동적 기능와 캐싱이 경로의 렌더링 동작에 미치는 영향을 정리했다.

| Data Fetching   | Dynamic Functions | Rendering |
| --------------- | ----------------- | --------- |
| Static (Cached) | No                | Static    |
| Static (Cached) | Yes               | Dynamic   |
| Not Cached      | No                | Dynamic   |
| Not Cached      | Yes               | Dynamic   |

캐시 여부와 무관하게 동적 함수에 따라 항상 동적 렌더링되는 것에 주목하자. 즉, 정적 렌더링은 데이터 fetching 동작뿐 아니라 동적 함수에도 의존한다.

> **Good To know**  
>
> 향후 Next.js는 route대신 레이아웃과 페이지를 독립적으로 정적 or 동적으로 렌더링할 수 있는 하이브리드 서버 측 렌더링을 도입할 예정이다.

### Dynamic Functions

동적 기능은 클라이언트 쿠키, 요청 헤더, URL Params 같이 요청 시 알 수 있는 정보에 의존한다. Next.js에서 이런 동적 기능은 다음과 같다.

- 서버 컴포넌트에서 `cookies()`또는 `headers()`를 사용하면 요청 시 전체 경로가 동적 렌더링으로 선택된다.

- 클라이언트 컴포넌트에서 `useSearchParams()`를 사용하면 정적 렌더링을 넘어 모든 클라이언트 컴포넌트를 클라이언트에서 가장 가까운 부모 Suspense boundary까지 렌더링한다.

  -  `useSearchParams()`를 사용하는 클라이언트 컴포넌트에서 `<Suspense/>` boundary로 감싸는 것이 좋다. 이렇게 하면 경계를 벗어난 클라이언트 컴포넌트는 정적 렌더링할 수 있다. 즉, 클라이언트 렌더링 범위를 줄일 수 있다. [Exmaple](https://nextjs.org/docs/app/api-reference/functions/use-search-params#static-rendering)를 참고하자.  

    예제 참고 : `useSearchParams()`를 사용하면 `Suspense`boundary 까지는 클라이언트 사이드 렌더링을 한다. 그래서 console.log()를 해보면 브라우저에 찍힐 것이다. 이를 사용함으로서 `searchParams()`를 사용하는 동적인 부분이 클라이언트에서 렌더링되는 동안 페이지 일부를 정적으로 렌더링할 수 있다.

- Pages에서 `searchParams` prop을 사용하면 요청 시 동적 렌더링할 수 있다.

---

## Dynamic Data Fetching

동적 data fetch는 `cache`옵션을 `no-store` 또는 `revalidate`를 0으로 설정해서 캐시 동작을 특별히 해제할 수 있는 `fetch()`요청이다.

layout 또는 page의 모든 `fetch` 요청에 대한 캐싱 옵션은 [segment config](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config) 개체를 사용해서 설정할 수 있다. 동적 Data Fetching에 대한 더 많은 정보는 [Data Fetching](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching)페이지를 참고하자.