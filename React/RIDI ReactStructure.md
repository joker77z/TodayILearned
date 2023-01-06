>  React에서 좋은 구조는 어떤 구조인가에 대해 항상 갈증이 있다. 그래서 틈 날때 마다 찾아보려고 하는데, 오늘은 리디에서 React를 사용하는 방법에 대해 알아보았다.

리디에서 components를 관리할 때 사용하는 구조는 아토믹 패턴의 변형된 형태다.
나도 아토믹 패턴에서 얘기하는 5가지(원자, 분자, 유기체, 템플릿, 페이지)가 너무 많아서 오히려 복잡성을 늘린다고 생각했는데, 리디에서는 그런 문제로 3가지(atoms, blocks, pages)로 줄여서 사용한다고 한다.

atoms에서는 하나의 컴포넌트가 하나의 기능을 담당하는 버튼같은 것들을 넣고, blocks에는 다른 페이지에서도 함께 사용되는 공통 컴포넌트들을, pages는 해당 페이지에서 사용하는 컴포넌트 및 스타일 코드들을 넣는다.

pages에 대해 조금 더 자세히 알아보면 Home 밑에 있는 index.tsx가 루트파일이 되고 styles.ts에는 스타일 파일들만 관리한다.

```jsx
pages/
  Home/
    index.tsx
    Home.tsx
    styles.ts
```

데이터 처리를 하고, API를 불러오고 하는 비즈니스로직 부분들은 index.tsx에 담겨있다. UI Module은 Home.tsx에 담겨있다. 즉 컨테이너형 컴포넌트 역할을 index.tsx에서 하고 안에 들어가는 컴포넌트가 Home.tsx다. Home.tsx에서 사용하는 하위 컴포넌트들은 Child.tsx를 만들어서 사용하고 있다고 한다.

# 참고

[리디_리액트 React로 만든 웹사이트](https://www.youtube.com/watch?v=exf4enLbVm4&t=1s)
