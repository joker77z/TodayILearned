> 리액트 컴포넌트는 만드는 방법이 굉장히 많다. 직접 만들어보지 않으면, 그 장단점은 알기 어렵다. 오늘은 그 많은 방법들로 직접 만들어보면서 장단점을 느껴보고자 한다.



# Button

Button컴포넌트는 어떤 서비스에서나 항상 만드는 컴포넌트다. 재사용성을 고려한 컴포넌트는 디자이너가 재사용이라는 개념을 알고 있어야 제대로 컴포넌트를 만들고 사용할 수 있는 것 같다.

> 만약, 지금 우리 회사처럼 디자이너가 이런 개념을 모를 수도 있기 때문에 두 가지 경우 다 만들어보자.

<br />

## 정형화된 디자인이 주어졌을 때 재사용할 수 있는 컴포넌트 디자인

> 우선적으로 알아볼 것은 디자이너가 재사용 가능한 설계를 이해하고 있다는 가정으로 알아보자.

전역 스타일을 사용하려면 scss를 루트파일인 App.tsx에서 불러오는 방법이 있고 styled-components를 사용하고 있다면 App.tsx에서 컴포넌트를 불러오는 것처럼 GlobalStyles파일을 만들어서 불러오는 방법이 있다.

### props로 sm, md, lg같은 size type 내려주기

props로 type을 내려준다.

```tsx
import { ReactNode } from "react";
import styled, { css } from "styled-components";

interface ButtonProps {
  disabled?: boolean;
  size: "sm" | "md" | "lg";
  children: ReactNode;
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

## 매번 다른 디자인! 재사용할 수 없는 디자인이 주어졌을 때

주어진 것은 색상탬플릿 뿐이고 크기와 색상이 쓰이는 곳마다 다르게 쓰고 있다면, 어쩔 수 없이 매번 만들 때마다 색상과 크기를 props로 내려줘야 할 것이다.

### props로 색상 palette에 있는 색상이름 내려주기

색상은 템플릿이 정해져있는 경우들이 있다. 이럴 때는 color template을 만들어놓는 것이 좋다. 그리고 객체인 color template을 `keyof typeof`를 이용해서 객체의 키들 중에서 선택한 type을 만들어낸다.

```tsx
> Button.tsx
import styled from 'styled-components';

const backgroundColor = {
  black: '#000000',
  white: '#ffffff',
};

type backgroundColorTypes = keyof typeof backgroundColor;

interface ButtonProps {
  backgroundColor: backgroundColorTypes;
  children: any;
}

const StyledButton = styled.button<ButtonProps>`
  background-color: ${props => backgroundColor[props.backgroundColor]};
  color: #ffffff;
  border: none;
  border-radius: 5px;
  padding: 12px 24px;
  margin: 0;
`;

export default function Button({ backgroundColor, children }: ButtonProps) {
  return (
    <>
      <StyledButton backgroundColor={backgroundColor}>{children}</StyledButton>;
    </>
  );
}
```

```tsx
> App.tsx
import React from 'react';
import Button from './components/Button';

function App() {
  return (
    <div className='App'>
      <Button backgroundColor='black'>이전</Button>
    </div>
  );
}

export default App;
```

<br />

# 후기

오늘은 제일 작은 원자단위 컴포넌트를 기준으로 디자이너가 "재사용 가능한 컴포넌트"에 대한 이해도가 있을 때와 없을 때로 접근해서 생각해봤다. 처음 리액트를 공부할 때 컴포넌트를 어떻게 만들어야 잘 만들까?를 고민을 많이 했다. 그런데, 리액트를 접한지 1년이 지난 지금 경험을 토대로 봤을 때 프론트엔드 개발자들끼리만으로 회사에서 재사용가능한 컴포넌트 생태계를 만들기 어렵다고 생각한다. 디자이너와 소통을 통해 일관된 컴포넌트를 설계할 수 있게끔 환경을 우리가 만들어 보는 것은 어떨까?

만약, 이미 완전 레거시라서 중구남방의 디자인들이 난무하고 있는 상황에서 리액트로 마이그레이션한다면 타입별로 아톰 컴포넌트를 정의하는 것보다 유연성이 높은 컴포넌트를 설계하는 것이 좋아 보인다.

새로 만드는 프로젝트라면 적당한 제한과 유연성이 존재하는 컴포넌트들을 설계한다면 유지보수가 좋을 것 같다. 그렇다고 타입을 딱 1종류로 정해서 primary, secondary 이런 방법은 좋지 않은 것 같다. 만약 타입이 10개가 나온다면? 참 난감하기 짝이 없을 것이다. 하지만, 사이즈, 색상, 텍스트 위치, 텍스트 크기, 테두리 여부 이런 것들을 타입으로 조절한다면 적당히 유연하면서도 제한된 환경에서 누구나 사용 가능할 것이다.

스토리북까지 같이 사용한다면 더 생산성이 올라가서 금상첨화겠다.





---

# 참고

https://www.daleseo.com/react-button-component/