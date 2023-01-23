> 리액트 컴포넌트는 만드는 방법이 굉장히 많다. 직접 만들어보지 않으면, 그 장단점은 알기 어렵다. 오늘은 그 많은 방법들로 직접 만들어보면서 장단점을 느껴보고자 한다.



# Button

Button컴포넌트는 어떤 서비스에서나 항상 만드는 컴포넌트다. 재사용성을 고려한 컴포넌트는 디자이너가 재사용이라는 개념을 알고 있어야 제대로 컴포넌트를 만들고 사용할 수 있는 것 같다.

> 만약, 지금 우리 회사처럼 디자이너가 이런 개념을 모를 수도 있기 때문에 두 가지 경우 다 만들어보자.

<br />

## 재사용할 수 있는 컴포넌트 디자인

> 우선적으로 알아볼 것은 디자이너가 재사용가능한 설계를 이해하고 있다는 가정으로 알아보자.

전역 스타일을 사용하려면 scss를 루트파일인 App.tsx에서 불러오는 방법이 있고 styled-components를 사용하고 있다면 App.tsx에서 컴포넌트를 불러오는 것처럼 GlobalStyles파일을 만들어서 불러오는 방법이 있다.

<br />

### props로 size나 success같은 상태 내려주기

props로 size나 success, error, warning같은 상태를 타입으로 내려줘서 각 타입에 맞는 스타일을 입힌다.

```tsx
import React from "react";
import styled, { css } from "styled-components";

interface ButtonProps {
  disabled?: boolean;
  size: "sm" | "md" | "lg";
  children: React.ReactChildren;
}

const SIZES = {
  sm: css`
    --button-font-size: 0.875rem;
    --button-padding: 8px 12px;
    --button-radius: 4px;
  `,
  md: css`
    --button-font-size: 1rem;
    --button-padding: 12px 16px;
    --button-radius: 8px;
  `,
  lg: css`
    --button-font-size: 1.25rem;
    --button-padding: 16px 20px;
    --button-radius: 12px;
  `
};

const StyledButton = styled.button`
  ${(props) => props.sizeStyle}

  margin: 0;
  border: none;
  cursor: pointer;

  font-size: var(--button-font-size, 1rem);
  padding: var(--button-padding, 12px 16px);
  border-radius: var(--button-radius, 8px);
`;

export default function Button({ disabled, size, children }: ButtonProps) {
  const sizeStyle = SIZES[size];

  return (
    <StyledButton disabled={disabled} sizeStyle={sizeStyle}>
      {children}
    </StyledButton>
  );
}
```

<br />

색상 팔레트같은 것은 createGlobalState를 이용해서 만들고 App.tsx에서 컴포넌트 사용하듯이 사용해준다.

```tsx
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
  :root {
    ...
  }
`;

export default GlobalStyle;

```

<br />

### props로 색상 palette에 있는 색상이름 내려주기







---

# 참고

https://www.daleseo.com/react-button-component/