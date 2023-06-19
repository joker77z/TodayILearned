[TOC]

> [Integrate Next.js and Storybook - Storybook](https://storybook.js.org/recipes/next)

---

## 설치

- 프로젝트에 storybook이 없는 상태일 때

  ```bash
  npx storybook@latest init
  ```

- 프로젝트에 storybook이 이미 있을 때
  ```bash
  npx storybook@latest upgrade
  ```

upgrade를 하는 경우 마이그레이션을 할꺼냐고 물어본다. 하지 않을 경우 수동으로 할 수 있다. 공식문서를 참고한다.

---

## Configuring next/navigation

Next.js13은 앱디렉토리와 함께 중첩된 라우트와 레이아웃을 지원한다. story가 앱 디렉터리의 컴포넌트를 사용하고 있고 이 컴포넌트가 next/navigation에서 모듈을 가져오는 경우, nextjs.appDirectory 매개 변수를 true로 설정하여 올바른 라우터 컨텍스트를 사용하도록 Storybook에 지시해야 합니다:

```js
export const Example = {
  parameters: {
    nextjs: {
      appDirectory: true,
    },
  },
};
```

Navigation Provider는 일부 기본값으로 구성되어 있다. override를 하려면 아래와 같이 하면된다.

```js
export const Example = {
  parameters: {
    nextjs: {
      appDirectory: true,
      navigation: {
        pathname: '/profile',
        query: {
          user: 'santa',
        },
      },
    },
  },
};
```

---

## Storybook + tailwindcss

tailwindcss를 사용하기 위해서 `app/globals.css`를 import한다. preview.ts에다가 추가해주면 된다.

`preview.ts`

```ts
import '../app/globals.css';
```

> 만약, root에 있는 `stories`폴더안 컴포넌트들에게 적용하고자 한다면 `tailwind.config.js`에 위치를 추가해줘야 한다.
>
> ```js
> /** @type {import('tailwindcss').Config} */
> module.exports = {
>   content: [
>     "./pages/**/*.{js,ts,jsx,tsx,mdx}",
>     "./components/**/*.{js,ts,jsx,tsx,mdx}",
>     "./app/**/*.{js,ts,jsx,tsx,mdx}",
>     "./stories/**/*.{js,ts,jsx,tsx,mdx}", // 이것!
>   ],
>   theme: {
>     ...
>   },
>   plugins: [],
> };
> 
> ```
>
> 