# Loading

`Suspense`에 loading 파일을 만들어서 로딩 상태를 만들 수 있다.

기본적으로 이 파일은 서버 컴포넌트지만 "use client"를 해서 클라이언트 컴포넌트로도 사용할 수 있다.

`/app/feed/loading.tsx`

```tsx
export default function Loading() {
  // Or Skeleton Component
  return 'Loading...'
}
```

> UI 구성 요소를 로드하는 중에는 매개변수를 사용할 수 없다.