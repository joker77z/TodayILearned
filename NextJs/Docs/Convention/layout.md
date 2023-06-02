# Layout

라우트에서 공유 UI를 만들 수 있다.

```tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return <section>{children}</section>;
}
```

RootLayout은 app 디렉토리 바로 하위에 있어야 하며, html과 body태그로 만들고 global하게 공유하는 UI다.

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

<br />

---

## Props

### children (필수)

레이아웃 컴포넌트는 children을 반드시 포함해야 한다.

### params(선택 사항)

루트 세그먼트에서 해당 레이아웃까지의 동적경로 매개변수 개체다.

| Example                           | URL            | `params`                  |
| --------------------------------- | -------------- | ------------------------- |
| `app/dashboard/[team]/layout.js`  | `/dashboard/1` | `{ team: '1' }`           |
| `app/shop/[tag]/[item]/layout.js` | `/shop/1/2`    | `{ tag: '1', item: '2' }` |
| `app/blog/[...slug]/layout.js`    | `/blog/1/2`    | `{ slug: ['1', '2'] }`    |

파일로 예를 들면 다음과 같다.

`app/shop/[tag]/[item]/layout.ts`

```tsx
export default function ShopLayout({
  children,
  params
}: {
  children: React.ReactNode;
  params: {
    tag: string;
    item: string;
  }
}) {
  // URL -> shop/shoes/nike-air
  // `params` -> { tag: 'shoes', item: 'nike-air-max-97' }
  return <section>{children}</section>
}
```

<br />

---

## Good To Know

**페이지와 달리 레이아웃 컴포넌트**는 `searchParams` prop을 받지 않는다. 이유는 예를 들어 Navigation Bar를 생각해보면 된다. 어떤 경로로 들어가던 항상 있는 공통 레이아웃 역할을 하기 때문이다. 예를 들어서 아래 디렉토리 구조를 보면 `dashboard/layout.tsx`는 `dashboard/settings`로 접속하나 `dashboard/analytics`로 접속하나 항상 동일한 공통 레이아웃을 보여줄 것이다. **이 때 공통 레이아웃은 리렌더링되지 않는다.** 이런 성능 최적화를 통해 속도를 더 올릴 수 있다. 이런 식으로 layout.tsx는 리렌더링되지 않기 때문에 searchParams의 상태는 오래 전 일 수 있다. 대신에 page에서 searchParams prop 또는 useSearchParams hook을 클라이언트 컴포넌트에서 사용함으로서 최신의 params를 얻을 수 있다.

## Root Layouts

- app 디렉토리는 반드시 app/layout.js 파일을 포함해야 한다.

- 이 때 root layout은 반드시 html, body 태그를 사용해야 한다.

  - 이 때, title이나 meta 태그같은 head 태그는 넣으면 안된다. 대신에 스트리밍이나 `<head>` 요소 중복제거 같은 고급 요구사항을 자동으로 처리하는 메타데이터 API를 사용해야 한다.

- route groups를 사용해서 여러 root layouts를 만들 수 있다.

  > route groups란 아래 사진과 같이 (shop)아래에 layout.js를 만들면 /account 나 /cart로 접근 시 동일한 layout을 렌더할 수 있다.
  >
  > ![Route Groups with Opt-in Layouts](../../images/route-group-opt-in-layouts.png)
  >
  > - app/layout.js를 제거할수도 있는데, 만약 제거한다면 각 root layout에서 `<html>`과 `<body>`태그를 사용해야 한다.
  > - 이 때 (shop)같은 이름은 구성 이외에 아무 의미가 없다. 즉, URL경로에 아무 영향을 주지 않는다.
  > - 만약, (marketing)/about/page.js 와 (shop)/about/page.js 둘 다 있다면 오류를 발생시킨다.
  > - 루트 레이아웃을 사용할 때 만약, 서로 다른 root layout으로 이동하는 경우에는 전체 페이지 로드가 발생한다.