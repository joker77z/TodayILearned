# not-found.js

not-found 파일은 notFound 함수가 세그먼트 내에서 실행되면 렌더링하는데 사용된다. 404 HTTP 상태도 반환된다. 해당 컴포넌트는 어떤 props도 받을 수 없다.

`app/blog/not-found.tsx`

```tsx
import Link from 'next/link';
 
export default function NotFound() {
  return (
    <div>
      <h2>Not Found</h2>
      <p>Could not find requested resource</p>
      <p>
        View <Link href="/blog">all posts</Link>
      </p>
    </div>
  );
}
```

> 일치하지 않은 URL도 처리한다.