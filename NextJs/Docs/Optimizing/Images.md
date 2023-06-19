[Web Almanac](https://almanac.httparchive.org/en/2022/page-weight#content-type-and-file-formats) 에 따르면 이미지는 페이지 무게의 큰 부분을 차지하고, LCP에 상당한 영향을 미친다고 한다.  

Next.js는 html의 `<img>`태그를 확장하여 자동으로 최적화한다.

- Size 최적화 : webp 및 avif 같은 최신 이미지 형식을 사용해서 각 장치에 최적화된 이미지를 제공한다.
- Visual Stability(시각적 안정성) : 이미지를 로드할 때 layout shift가 일어나는 것을 자동으로 방지한다.
- 더 빠른 페이지 로드 : viewport로 진입할 때 lazy loading을 사용하고 blur-up placeholders를 선택할 수 있다.
- Asset Flexibility: remote server에 저장된 이미지에 대해서도 이미지 리사이징을 한다.

---

## Usage

```tsx
import Image from 'next/image';
```

이렇게 정의를 하고 `src`에 이미지 주소를 넣으면 된다. (local or remote)

### Local Images

Next.js는 가져온 파일을 기준으로 이미지의 너비, 높이를 자동으로 결정한다. 이런 값은 이미지를 로드하는 동안 [CLS](https://nextjs.org/learn/seo/web-performance/cls)를 방지하는데 사용된다.

```js
import Image from 'next/image'
import profilePic from '../public/me.png'
 
export default function Page() {
  return (
    <Image
      src={profilePic}
      alt="Picture of the author"
      // width={500} automatically provided
      // height={500} automatically provided
      // blurDataURL="data:..." automatically provided
      // placeholder="blur" // Optional blur-up while loading
    />
  )
}
```

> 동적 `await import()`나 `require()`는 지원하지 않는다. `import`는 빌드 시에 분석할 수 있도록 정적이어야 한다.

### Remote Images

Remote 이미지는 src에 string이 들어와야 한다. 빌드 중에 Next.js는 Remote에 있는 파일에 액세스 할 수 없기 때문에 너비, 높이 및 옵션인 `blurDataURL`을 수동으로 제공해야 한다. 너비, 높이는 세로 가로 비율을 추론할 수 있게 하고 Layout Shift를 방지하는데 쓰인다. **렌더링된 크기를 결정하지 않는다.**

```tsx
import Image from 'next/image'
 
export default function Page() {
  return (
    <Image
      src="https://s3.amazonaws.com/my-bucket/profile.png"
      alt="Picture of the author"
      width={500}
      height={500}
    />
  )
}
```

이미지를 안전하게 최적화하기 위해서 `next.config.js`에 패턴 list를 정의할 수 있다. 악의적인 사용을 방지하기 위해 가능한 구체적으로 지정할 것을 권한다. 예를 들어 다음 예제에서는 aws의 s3 버킷 이미지만 허용한다.

```js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 's3.amazonaws.com',
        port: '',
        pathname: '/my-bucket/**',
      },
    ],
  },
}
```

### Loaders

URL에 `/me.png`만 쓰는 식으로 remote image를 불러올 수 있는 방법이 있다. 이미지 별로 [loader](https://nextjs.org/docs/app/api-reference/components/image#loader) 속성을 사용하는 방법과 어플리케이션 단에서 [loaderFile configuration](https://nextjs.org/docs/app/api-reference/components/image#loaderfile)을 사용하는 방법이 있다.

---

## Priority

각 페이지에 대해 LCP 요소가 될 이미지에 우선 순위 속성을 부여할 수 있다. LCP 요소가 우선 순위 속성이 없는 `<Image`일 경우 콘솔로 경고가 표시된다. LCP 이미지를 식별했다면 아래와 같이 [priority](https://nextjs.org/docs/app/api-reference/components/image#priority) 속성을 추가할 수 있다.

```js
import Image from 'next/image'
import profilePic from '../public/me.png'
 
export default function Page() {
  return <Image src={profilePic} alt="Picture of the author" priority />
}
```

---

## Image Sizing

이미지가 성능 저하 시키는 방법 중 하나는 레이아웃 쉬프트로 인해 로드될 때 다른 요소를 미는 것이다. 이 성능 문제는 성가신 문제로 코어 웹 바이탈에서 LCS를 별도로 가지고 있을 정도다. 이것을 방지하는 방법은 이미지 크기를 항상 조정하는 것이다. 이를 통해 브라우저는 로드하기 전 이미지를 위한 공간을 예약해둘 수 있다.  

이미지를 조정하는 세 가지 방법 중 하나로 조정할 수 있다.

1. 정적 리소스를 import하는 방법 사용
2. 명시적으로 width나 height 속성을 명시
3. `fill`속성을 사용하여 암묵적으로 부모 요소에 채우는 방법

> **만약 size를 모르면 어떻게 할까?**  
>
> 이미지에 [fill](https://nextjs.org/docs/app/api-reference/components/image#fill) 속성을 true값으로 주면 부모 요소에 맞춰 크기를 조정한다. `sizes`속성에 미디어쿼리를 줘서 제공하는 것도 고려할 수 있다. 또한 `object-fit`, `object-position`으로 위치와 차지하는 방식을 정할 수 있다.

---

## Styling

- `className`또는 `style`사용하기. `styled-jsx`는 안된다. 이유는 `styled-jsx`는 현재 컴포넌트만 scope를 형성하기 때문이다. (style을 `global`에 만들었을 때는 예외)
- `fill`을 사용한다면 부모 요소는 반드시 `position:relative`여여야 한다. 그리고 `display: block`이어야 한다.

---

- **모든 프로퍼티는 [API공식문서](https://nextjs.org/docs/app/api-reference/components/image)에서 확인할 수 있다.**
- **예제는 [이곳](https://image-component.nextjs.gallery/)에서 확인할 수 있다.**
