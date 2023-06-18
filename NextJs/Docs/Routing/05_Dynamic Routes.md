# Dynamic Routes (동적 경로)

정확한 세그먼트를 미리 알지 못하고, 동적 데이터에서로부터 경로를 만들려는 경우 요청 시 입력되거나 빌드 시 미리 렌더링된 동적 세그먼트를 사용할 수 있다.

<br />

## 컨벤션

동적 세그먼트는 폴더 이름을 대괄호로 묶어서 만들 수 있다. 예를 들면, [id]나 [slug]처럼 [folderName]을 만들면 params로 전달된다. 이 동적 세그먼트는 props로 layout, page, route, generateMetadata 함수로 전달할 수 있다.

<br />

## 예제

예를 들어, blog는 다음 경로를 포함할 수 있다.

`/app/blog/[slug]/page.js`

```ts
export default function Page({ params }) {
  return ...
}
```

| 경로                      | 예제 URL  | `params`        |
| ------------------------- | --------- | --------------- |
| `app/blog/[slug]/page.js` | `/blog/a` | `{ slug: 'a' }` |
| `app/blog/[slug]/page.js` | `/blog/b` | `{ slug: 'b' }` |
| `app/blog/[slug]/page.js` | `/blog/c` | `{ slug: 'c' }` |

> - 바로 아래에 나오는 `generateStaticPrams()`를 통해 세그먼트에서 params를 만드는 방법을 배운다.
> - 동적 세그먼트는 동적 라우트와 `pages` 디렉토리에서 동일하다.

<br />

## Generating Static Params

`generateStaticParams` 함수를 동적 라우트 세그먼트와 함께 사용해서 요청이 있을 때 경로를 만드는 것이 아니라 빌드할 때 정적으로 생성할 수 있다.

`app/blog/[slug]/page.tsx`

```tsx
export async function generateStaticParams() {
  const posts = await fetch('https://.../posts').then((res) => res.json());
  
  return posts.map((post) => ({
    slug: post.slug
  }))
}
```

`generateStaticParams`의 주요 이점은 데이터를 스마트하게 받아온다는 것이다. 만약, 해당 함수 내에서 컨텐츠를 가져오면 중복 요청이 자동으로 제거된다. 이 말은 generateStaticParams 함수, Layout들, Page들에서 오직 1번만 요청할 수 있게끔하여 빌드 시간을 줄일 수 있다.

<br />

## Catch-all Segements

다이나믹 세그먼트는 [... folderName]과 같은 형태를 통해 모든 sub 세그먼트로 확장할 수 있다.

| Route                        | Example URL   | `params`                    |
| ---------------------------- | ------------- | --------------------------- |
| `app/shop/[...slug]/page.js` | `/shop/a`     | `{ slug: ['a'] }`           |
| `app/shop/[...slug]/page.js` | `/shop/a/b`   | `{ slug: ['a', 'b'] }`      |
| `app/shop/[...slug]/page.js` | `/shop/a/b/c` | `{ slug: ['a', 'b', 'c'] }` |

<br />

## Optional Catch-all Segements

[...slug]를 사용하는 Catch-all Segements와 차이점은 /shop뒤에 path가 없는 경우 빈 객체로 받는다는 것이다.

| Route                          | Example URL   | `params`                    |
| ------------------------------ | ------------- | --------------------------- |
| `app/shop/[[...slug]]/page.js` | `/shop`       | `{}`                        |
| `app/shop/[[...slug]]/page.js` | `/shop/a`     | `{ slug: ['a'] }`           |
| `app/shop/[[...slug]]/page.js` | `/shop/a/b`   | `{ slug: ['a', 'b'] }`      |
| `app/shop/[[...slug]]/page.js` | `/shop/a/b/c` | `{ slug: ['a', 'b', 'c'] }` |

<br />

## Typescript

`app/blug/[slug]/page.tsx`

```tsx
export default function Page({ params }: { params: { slug: string } }) {
  return <h1>My Page</h1>;
}
```

| Route                               | `params` Type Definition                 |
| ----------------------------------- | ---------------------------------------- |
| `app/blog/[slug]/page.js`           | `{ slug: string }`                       |
| `app/shop/[...slug]/page.js`        | `{ slug: string[] }`                     |
| `app/[categoryId]/[itemId]/page.js` | `{ categoryId: string, itemId: string }` |

<br />

## Next Steps

- [Linking and Navigating](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating)
- [generateStaticParams](https://nextjs.org/docs/app/api-reference/functions/generate-static-params)