> 어제는 React의 context API를 사용하지 않고 합성 컴포넌트를 구현했다. 오늘은, contextAPI를 사용해서 구현해보고 두 개 중 어떤 장단점이 있는지 알아보려고 한다.



# 요구사항

- Shuffle 버튼을 누르면 랜덤 숫자가 나온다.
- 랜덤 숫자의 min, max를 정할 수 있다.
- reset버튼을 누르면 0으로 숫자가 초기화된다.
- input 요소에 랜덤 숫자가 나온다.



# 구현해보자

먼저 Shuffle이란 컴포넌트를 사용하는 곳부터 보자.

랜덤 기능은 Shuffle 내 Button 컴포넌트에서 useContext를 이용하여 onSuffle이란 함수를 받을 것이고 Shuffle된 숫자를 반환할 것이다.

```jsx
> src/App.js

import { useState } from "react";
import Button from "./components/common/Button";
import Input from "./components/common/Input";
import Shuffle from "./components/Shuffle/Shuffle";
import "./styles.css";

export default function App() {
  const [value, setValue] = useState(0);

  const handleSuffle = (value) => {
    setValue(value);
  };

  const handleReset = () => {
    setValue(0);
  };

  return (
    <div className="App">
      <Shuffle
        value={value}
        min={10}
        max={100}
        onShuffle={handleSuffle}
        onReset={handleReset}
      >
        <Input type="number" disabled />
        <Button type="button">Shuffle</Button>
        <Button type="reset">Reset</Button>
      </Shuffle>
    </div>
  );
}
```



<br />

하위 컴포넌트인 Input이나 Button으로 context를 제공한다.

```jsx
> src/components/Shuffle/Shuffle.jsx

import React, { createContext, useState } from "react";

export const shuffleContext = createContext(0);

export default function Shuffle({
  value,
  min,
  max,
  onShuffle,
  onReset,
  children
}) {
  const contextValue = {
    value,
    min,
    max,
    onShuffle,
    onReset
  };

  return (
    <shuffleContext.Provider value={contextValue}>
      {children}
    </shuffleContext.Provider>
  );
}

```



  <br />

type이나 disabled같은 속성들은 spread된 others를 통해 들어온다.

```jsx
> src/components/common/Input.jsx

import { useContext } from "react";
import { shuffleContext } from "../Shuffle/Shuffle";

export default function Input({ ...others }) {
  const ShuffleContext = useContext(shuffleContext);

  return <input value={ShuffleContext.value} {...others} />;
}
```

  <br />

 Button컴포넌트에서 랜덤으로 만든 숫자를 onShuffle함수의 인수로 보낸다.

> type으로 shuffle할지, reset할지 나누는 것은 좋지 않은 코드지만 지금은 compound Component에서 context를 사용하는 것의 장단점을 알아보기 위해서다.

```jsx
> src/components/common/Button.jsx

import { useContext } from "react";
import { shuffleContext } from "../Shuffle/Shuffle";

export default function Button({ type, children }) {
  const ShuffleContext = useContext(shuffleContext);
  const { min, max, onShuffle, onReset } = ShuffleContext;

  const handleClick = () => {
    if (type === "button") {
      const value = Math.floor(Math.random() * (max - min + 1) + min);
      onShuffle(value);
    }
    if (type === "reset") {
      onReset();
    }
  };

  return (
    <button type={type} onClick={handleClick}>
      {children}
    </button>
  );
}
```





# 후기

이렇게 만들고보니 드는 생각은 공통 컴포넌트들인 Input, Button에서 useContext를 통해 함수, 값을 전달받고 있는 형태라 사용처가 공급자 내에 있어야 하기 때문에 제한된다는 느낌을 받았다.

하지만, 만약 Shuffle밑에 atom 컴포넌트가 아니라 분자형이나 템플릿형 컴포넌트여서 props drilling이 발생하는 경우라면 지금과 같이 context를 써서 전달하는 것이 더 나을수도 있겠다라는 생각은 했다.

그 외의 경우라면 코드도 더 짧고 직관적으로 알기도 쉽고 공통컴포넌트도 재활용하기 쉬운 [이 방식](./Compound Component.md) 이 낫지 않을까..?



# 전체 코드보기

[코드 샌드박스](https://codesandbox.io/s/brave-shirley-s2ulrp?file=/src/App.js)

# 참고

[React Pattern - Compound Components](https://flowergeoji.me/react/react-pattern-compound-components/)