# Page

`app/blog/[slug]/page.tsx`

```ts
export default function Page({
  params,
  searchParams,
}: {
  params: { slug: string };
  searchParams: { [key: string]: string | string[] | undefined };
}) {
  return <h1>My page</h1>;
}
```

<br />

---

## Props

### params (선택사항)

루트 세그먼트에서부터 해당 페이지까지의 동적 경로를 포함하고 있는 객체다.

| Example                              | URL         | `params`                       |
| ------------------------------------ | ----------- | ------------------------------ |
| `app/shop/[slug]/page.js`            | `/shop/1`   | `{ slug: '1' }`                |
| `app/shop/[category]/[item]/page.js` | `/shop/1/2` | `{ category: '1', item: '2' }` |
| `app/shop/[...slug]/page.js`         | `/shop/1/2` | `{ slug: ['1', '2'] }`         |



### searchParams (선택사항)

현재 URL의 파라미터를 포함하고 있는 객체다.

| URL             | `searchParams`       |
| --------------- | -------------------- |
| `/shop?a=1`     | `{ a: '1' }`         |
| `/shop?a=1&b=2` | `{ a: '1', b: '2' }` |
| `/shop?a=1&a=2` | `{ a: ['1', '2'] }`  |
