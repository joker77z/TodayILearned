> 나는 오늘 두 가지 초점에 맞춰 컴포넌트를 설계하는 방법을 공부하고자 하였다.

- 기획이나 디자인은 상황에 따라 언제든지 바뀔 수 있기 때문에 언제나 변화할 수 있는 형태의 컴포넌트를 만들고 싶다.
- children으로 무작정 만들면서 과한 자유도는 다른 동료 개발자가 어떻게 이 컴포넌트를 사용해야 하는지 헤깔릴 수 있기 때문에 어느정도의 정해진 사용법을 만들고 싶다.



# 문제의 발단

현재 회사에 있는 공통 컴포넌트들은 대부분 children으로 내리도록 되어있다. 이렇게 설계되어있다보니 너무 높은 자유도로 인해 각자 다 다른 컴포넌트들이 개발자마다 다른 코드로 결과적으로 만들어지고 있었다.

이를 해결해 줄 하나의 방법으로 합성 컴포넌트가 있다.



# 해결 방법

우선 제목과 설명 버튼들을 가져야 하는 버튼그룹을 만들어야 한다고 가정해보자.
제목, 설명, 버튼들의 위치는 언제든지 바뀔 수 있다.
제목이 제일 위에 있는 버튼 그룹과 제목이 제일 아래 있는 컴포넌트를 만들어보자.

## 구조

구조는 동료개발자가 메인 컴포넌트와 서브 컴포넌트들을 구별하게 하기 위해 아래와 같이 설계해봤다.

동료 개발자는 ButtonGroup폴더로 들어갔을 때 TopTitleButtonGroup.js 파일이나 BottomTitleButtonGroup이란 파일을 먼저 보게되고 wrapper로 사용해야 하는 메인 컴포넌트라는 것을 알 수 있을 것이다.

이어서 subComponents폴더를 보게 되고 여기에는 Wrapper역할을 하는 메인 컴포넌트에 서브로 들어갈 컴포넌트라는 것을 직감할 수 있을 것이다.

> 동료 개발자들이 이렇게 이해할 것이라고 단정짓는 것은 위험하다. 하지만, 일단 내가 생각했을 때 별도의 문서로 정리하지 않아도 이 방법대로라면 Compound Component를 쓸 때 이해하기 쉬울 것 같아서 정리해보았다.

```js
> 구조

📂src
--📂components
--📂buttonGroup
----📄TopTitleButtonGroup.js
----📄BottomTitleButtonGroup.js
----📂subComponents
------📄ButtonGroup.js
------📄ButtonGroupTitle.js
------📄ButtonGroupDescription.js

```



## 코드

이제 어떤 식으로 코드들을 작성했는지 살펴보자.
무관하게 코드가 어떤 식으로 작성되는지 알 수 있도록 하기 위해 스타일 내용은 제외했다.



### 사용하는 곳

먼저 컴포넌트를 사용하는 곳에서 코드다.
TopTitleButtonGroup 컴포넌트 이름으로 title이 어떤 위치에 해당할지 명시적으로 알려 주었다.
(만약 복잡한 형태를 가진 컴포넌트라면 이름도 복잡해질 것이고 이 예제와 같이 쉽게 해결되지 않을 것이다. 그럴 경우 Storybook이나 별도의 문서로 정리해놓는 것이 좋을 것 같다.)

```jsx
> src/App.jsx
  
import TopTitleButtonGroup from "./components/ButtonGroups/TopTitleButtonGroup";

export default function App() {
  return (
    <div className="App">
      <TopTitleButtonGroup title="제목" description="설명">
        <button type="button">버튼</button>
      </TopTitleButtonGroup>
    </div>
  );
}
```



### 컴포넌트

이제 컴포넌트들이 어떻게 설계되었는지 살펴보자.

먼저, 메인이 될 TopTitleButtonGroup 컴포넌트다.
Wrapper역할을 할 ButtonGroup 컴포넌트 안에 SubComponent들의 위치를 이곳에서 지정해준다.

```jsx
> src/components/ButtonGroup/TopTitleButtonGroup.jsx

import ButtonGroup from "./SubComponents/ButtonGroup";
import ButtonGroupDescription from "./SubComponents/ButtonGroupDescription";
import ButtonGroupTitle from "./SubComponents/ButtonGroupTitle";

export default function TopTitleButtonGroup({ title, description, children }) {
  return (
    <ButtonGroup>
      <ButtonGroup.title title={title} />
      <ButtonGroup.description description={description} />
      {children}
    </ButtonGroup>
  );
}

```



다음으로, 서브 컴포넌트들을 살펴볼 차례다.

서브컴포넌트들 중 Wrapper역할을 하는 컴포넌트다.
ButtonGroup 속성에 title과 description키, 값을 연결하는 부분이 합성 컴포넌트의 핵심이다.

```jsx
> src/components/ButtonGroup/SubComponents/ButtonGroup.jsx

import ButtonGroupDescription from "./ButtonGroupDescription";
import ButtonGroupTitle from "./ButtonGroupTitle";

function ButtonGroup({ children }) {
  return <div style={{ border: "1px solid black" }}>{children}</div>;
}

ButtonGroup.title = ButtonGroupTitle;
ButtonGroup.description = ButtonGroupDescription;

export default ButtonGroup;
```



이어서, ButtonGrouopTitle과 ButtonGroupDescription이다.

```jsx
> src/components/ButtonGroup/SubComponents/ButtonGroupTitle.jsx

export default function ButtonGroupTitle({ title }) {
  return <h2>{title}</h2>;
}
```

```jsx
> src/components/ButtonGroup/SubComponents/ButtonGroupDescription.jsx

export default function ButtonGroupDescription({ description }) {
  return <p>{description}</p>;
}
```



# 결과

결과적으로 컴포넌트의 복잡도를 낮추어 깔끔하게 작성할 수 있게 되었고, 적은 결합도를 유지하면서 공통 관심사를 모을 수 있었다. 더이상 컴포넌트들이 서로 복잡하게 엮이지 않고, 각 역할마다 명확하게 컴포넌트를 분리할 수 있어서 재사용성이 높고 유지 보수성이 좋아졌다.



# 전체 코드 보기

[코드 샌드박스](https://codesandbox.io/s/peaceful-wright-tobnm7?file=/src/components/ButtonGroups/TopTitleButtonGroup.js)



# 참고

[벨로그-예제로 알아보는 Compound Component](https://velog.io/@operqudgns/%EC%8B%A4%ED%99%94-%EA%B0%99%EC%9D%80-%EC%83%81%ED%99%A9%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-Compound-Component)

