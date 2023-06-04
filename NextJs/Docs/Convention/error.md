# error.js

오류 파일은 라우트 세그먼트에 대한 오류 UI 경계를 정의한다. page에서 에러가 발생하게 되면 상위 레이아웃들은 상태를 유지하고, 인터렉티브한 상태를 지속한다. 에러컴포넌트는 리커버링 될 수 있도록 기능을 제공해야 한다. reset함수는 리렌더링을 시도한다.  

데이터를 가져오는 도중이나 서버 컴포넌트 안에서 발생한 에러의 경우 Next.js는 `Error` 객체를 가장 가까운 error.js 파일에 `error` prop으로 전달한다.  

next dev가 실행되는 동안에는 error 객체가 서버 컴포넌트로부터 error..js로 전달되면서 시리얼라이즈 되고, 

`app/message/error.tsx`

```tsx
'use client'; // 에러 컴포넌트는 반드시 Client Components여야 한다.

import { useEffect } from 'react';

export default function Error({
  error,
  reset
}: {
  error: Error;
  reset: () => void;
}) {
  useEffect(() => {
    // 에러 리포팅 서비스에 에러 로그를 기록.
    console.log(error);
  }, [Error]);
 
  return (
    <div>
      <h2>someting went wrong!</h2>
      <button onClick={() => reset()}Try Again</button>
    </div>
  )
}
```

<br />

## Props

- `error`

  서버 또는 클라이언트에서 발생할 수 있는 에러 인스턴스

- `reset`

  error boundary를 reset하는 함수다. 이 함수는 반환하는 값이 없다.

### Good To Know

에러 바운더리는 레이아웃 컴포넌트안에 중첩되있어있기 때문에 error.js 바운더리는 같은 세그먼트에 있는 layout.js 에서 던진 오류는 처리하지 않는다. page.js나 중첩된 자식 세그먼트를 감싸게 된다.

- 특정 layout에 대한 오류를 처리하려면 레이아웃 상위 세그먼트에 error.js 파일을 배치한다.
- root layout이나 template 내의 에러를 처리하려면 `app/global-error.js`라는 error.js의 변형을 사용해야 한다.

<br />

## global-error.js

root의 layout.js의 오류를 구체적으로 처리하려면  error.js의 변형인 `app/global-error.js`를 app디렉토리에서 사용해야 한다.

`app/global-error.tsx`

```tsx
'use client';

export default function GlobalError({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <html>
      <body>
        <h2>Something went wrong!</h2>
        <button onClick={() => reset()}>Try again</button>
      </body>
    </html>
  );
}
```

### Good To Know

global-error.js는 활성화 될 때 root layout.js를 대체하므로  `<html>` 및 `<body>`태그를 정의해야 한다.

- global-error.js는 catch-all 에러 핸들링 UI가 될 수 있고, 가장 덜 조밀한 에러 UI다. 루트 컴포넌트들은 덜 동적이기 때문에, 자주 발생하지는 않을 것이다. 대부분 다른 error.js 바운더리들이 에러를 캐치할 것이다. 



> 만약, 에러 페이지에서 다시 리렌더링할 수 있는 click이벤트가 없다면 사용자는 리로드를 해야하고 전체 리로드를 하게 된다. 따라서 버튼을 제공하여 전체 리로드 하는 일을 막아야 한다.

> **참고하면 좋은 사이트**  
>
> [velog에서 정리 잘 해놓은 글](https://velog.io/@chaewonkang/Next.js-13-1.-Routing-1.6.-Error-Handling)