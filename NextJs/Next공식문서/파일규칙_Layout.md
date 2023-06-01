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

**페이지와 달리 레이아웃 컴포넌트**는 `searchParams` prop을 받지 않는다. 이유는 예를 들어 Navigation Bar와 같이 