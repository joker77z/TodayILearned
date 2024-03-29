> 최근 프로젝트에서 Storybook을 사용한 경험이 있다. 그런데, 잘 사용한 것인가? 의문점은 남았었고, 어떻게 분리를 해야 Storybook을 더 잘 사용할 수 있을지 고민이었다. 카카오 기술블로그에서 스토리북을 사용하고 경험을 적은 것이 있어서 정리해보기로 했다.
> 오늘 글은 [스토리북 작성을 통해 얻게 되는 리팩토링 효과](https://fe-developers.kakaoent.com/2022/220609-storybookwise-component-refactoring/) 를 내가 이해하기 쉽게 정리한 글이다.



# 스토리북의 장점1 : UI 미리보기

컴포넌트 주도 개발을 할 때 어떤 조건을 만족했을 때 재현되는 컴포넌트의 경우 재현이 번거로워진다. 예를 들어, 결제를 한 뒤 보여야 할 모달이 있다면 매번 결제를 하거나 어떤 변수를 수정해서 결제가 된 모달화면을 확인해야 한다. 하지만, 스토리북을 사용하면 컴포넌트만 별도의 환경에서 확인할 수 있어 유용하다.

동료 개발자가 코드리뷰를 해줄 때나 디자이너에게 디자인 피드백 요청을 하려고 할 때도 유용하다.

<br />

# 스토리북의 장점2 : 좀 더 나은 컴포넌트 구현에 신경을 쓰게 된다.

코드를 작성할 때는 나름 잘 만들었다고 생각하지만, 나중에 수정 및 재사용을 하려고 보면 설계의 문제점이 발견되고는 한다. 그런데, 컴포넌트 구현과 스토리 작성을 병행하게 되면 스토리로 옮기는게 예상대로 되지 않아 자연스레 설계의 결함을 발견하게 된다. 그래서 스토리 작성을 위해 고민을 하다보면 리팩토링으로 이어진다.

좀 더 구체적으로 얘기해보자. 스토리는 다음과 같은 특징을 갖는다.

- 독립적인 환경을 갖는 컴포넌트
- 재사용할 수 있는 컴포넌트

다른 컴포넌트와 얽히지 않고 독립적인 환경을 갖는 컴포넌트와 재사용 할 수 있는 컴포넌트를 스토리로 만들기 때문에 이 기준에 맞춰서 우리는 컴포넌트를 만들다보면 자연스레 리팩토링을 하게 되는 것이다.

> 우리는 일이 바쁘다는 핑계로 리팩토링을 미루기도 한다. 스토리북을 사용해봄으로서 리팩토링을 강제해보는 것은 어떨까?

<br />

# 스토리북을 활용하여  나아진 사례1 : 하나의 컴포넌트에서 너무 많은 일을 하는 경우

하나의 컴포넌트가 데이터 패치, 라우팅 처리, 상태 관리, 로깅 데이터 전송 등 많은 사이드 이펙트가 있는 일을 수행하고 있었다. 스토리북으로 옮기니 에러가 났다. 스토리북은 별도의 독립적인 환경이기 때문에 처리하기 위한 모킹작업이 필요하다. 

컴포넌트의 '렌더링'역할은 모킹하지 않아도 잘 동작한다. 우리는 이 렌더링 역할에 집중하여 Presentational한 컴포넌트로 분리해야 한다.

> 이 때 주의할 점은 너무 과하게 작은 컴포넌트로 만들지 않는 것이다. 재사용이 가능하거나 시멘틱으로 구분할 수 있는 이해하기 쉬운 단위면 충분하다.

```jsx
/********************* BEFORE *********************/
const Human = () => {
  // data fetching
  const [face, isLoading] = useFetch('face');
  // routing
  const router = useRouter();
  // state managing
  const health = useRecoilValue(healthAtom);
  // ... many side effects

  return (
    <>
      {'...'}
      <>
        {'...'}
        {face.eyes}
        {'...'}
      </>
      {'...'}
    </>
  );
}

/********************* AFTER *********************/
const Human = () => {
  // data fetching
  const [face, isLoading] = useFetch('face');
  // routing
  const router = useRouter();
  // state managing
  const health = useRecoilValue(healthAtom);
  // ... many side effects

  return (
    <>
      {'...'}
      <Eyes eyes={eyes} />
      {'...'}
    </>
  );
};

// 📌 Presentational한 컴포넌트로 분리!
const Eyes = ({ eyes }) => {
  return (
    <>
      {'...'}
      {eyes}
      {'...'}
    </>
  );
};
```

<br/>

## 데이터를 불러오는 경우

컴포넌트에서 데이터를 불러온다면, 스토리북에서도 데이터 모킹이 필요하다. 모킹을 추가하는 것은 피곤한 일이다. 그래서 스토리를 작성하다보면 **이 컴포넌트에서 데이터를 불러오는 것이 꼭 필요한지 재검토해보게 된다.** 

예를 들어, lazy loading을 하는 컴포넌트를 구현한다고 했을 때 Container/Presentational를 구분해서 분리하면 모킹을 할 필요가 없어져 스토리 작성이 쉬워진다.

> 지금 생각해보면 이런 상황에 컴포넌트가 아닌 페이지는 어떻게 구현해야될까?에 대해 고민을 많이 했었다. 일단 페이지에 집중하기 보다는 하나의 작은 컴포넌트 단위들로 스토리를 좀 더 쉽게 만들할 수 있는 방법에 집중하는 것이 좋을 것 같다.

```.jsx
/********************* BEFORE *********************/
const Face = () => {
  // data fetching
  const [face, isLoading] = useFetch('face');
  return isLoading ? (
    <Loading />
  ) : (
    <>
      {'...'}
      {face}
      {'...'}
    </>
  );
};


/********************* AFTER *********************/
// 📌 Suspense로 감싸서 로딩을 처리했다.
const FaceLoader = () => {
  return (
    <Suspense fallback={<Loading />}>
      <FaceFetcher />
    </Suspense>
  );
}

// 📌 Fetcher를 분리했다. 
// 커스텀 훅을 사용하는 방법도 있겠지만 이런 식으로 Fetcher Container로 따로 구별하는 것도 좋아보인다.
const FaceFetcher = () => {
  const [face] = useFetch('face');
  return <Face face={face} />;
};

// 📌 스토리가 될 컴포넌트다.
const Face = ({ face }) => {
  return (
    <>
      {'...'}
      {face}
      {'...'}
    </>
  );
};
```

before와 after를 구별해보면 좀 더 구조가 명확하고 이해하기가 쉽다. 특히, 커스텀 훅이 아닌 fetcher 컴포넌트를 별도로 만든 것이 인상적인데, 이렇게 해서 하나의 컴포넌트에서 의존성이 내부에 캡슐화되어 있다가 외부에서 주입하는 형태로 바뀌었다. Face컴포넌트에서는 데이터를 어떻게 불러오는지는 관심사 밖이 되었고, 잘 그리는 일에만 집중할 수 있게 되었다.

> 이제 storybook control패널에서 props를 바꿔주기만 하면 손쉽게 UI를 확인할 수 있다!!

결과적으로 이와 같은 개선으로 인해 스토리북에 올리기도 쉬워졌고 유지보수성도 좋아졌다.

<br/>

# 스토리북을 활용하여 나아진 사례2 : 그려서는 안되는 결과물로 그릴 경우

컴포넌트에 많고 타입이 명확하지 않은 variation(props)이 주어지게 되면 컴포넌트의 재활용성은 올라가고 유연성이 좋아진다. 하지만, 재사용을 잘못하면 결함이라고 느낄 수 있는 상황이 벌어지기도 한다. 즉, 재사용 범위가 넓어진 만큼, 잘 사용하기는 어려워지고 오히려 재사용성이 떨어지는 결과가 발생한다.

## 첫번째 예

예를 들어보자. 발행일을 보여주는 publishedDate의 경우 사진처럼 YYYY.MM.DD와 같은 형식으로 출력되기를 바랬지만 22/06/29 13:50 이런 식으로 사용될 가능성도 있다. 

![챕터](https://fe-developers.kakaoent.com/static/b75e6b29043ee576f049729fae78d564/f058b/chapter-normal.png)



Date타입을 사용했다면 이런 상황을 방지할 수 있다.

![Headwind example](https://fe-developers.kakaoent.com/static/bd8627dc8503776cc8cda898021fde92/f058b/chapter-date.png)

<br />

## 두번째 예

얼굴을 두 가지 방식으로 그려보았다.

```jsx
const Face = ({ eyesY }: { eyesY: number }) => {...}
```

```jsx
const Face = ({ eyesY }: { eyesY: 'top' | 'middle' | 'bottom' }) => {...}
```

전자의 방식으로 컴포넌트를 렌더링 한다면, 눈이 얼굴보다 더 아래에 있는 상황이 만들어질 수도 있다. 그러나 아래와 같이 정해진 틀을 제공한다면 그럴일이 없다. 하지만, 이렇게 제한하는 것이 무조건 좋은 것은 아니다. 유연성이 늘어날수록 재사용성이 좋아진다. 이렇게 하기 위해서는 **유연성이 높은 컴포넌트들을 먼저 만들고 다음으로 목적과 용도에 맞게 유연성이 낮은 컴포넌트로 조립해가는 것이 베스트라고 말한다.**

<br />

## 컴포넌트의 유연성을 확장하기 위한 패턴

### 방법1 : 스타일을 외부에서 주입하기

대표적인 예시는 스타일을 외부에서 주입하는 것이라고 한다. 아래 예시는 classes 라는 props에 tailwind 클래스로 스타일을 주입하는 예시다. 스타일 충돌 처리를 위해tailwind merge를 사용했다.

```jsx
const Face = ({ classes }) => {
  return (
    <>
      <Eyes className={classes.eyes} />
      <Nose className={twMerge('mx-auto', classes.nose)} />
      <Mouse className={twMerge('bg-red', classes.mouth)} />
    </>
  );
};
```

<br />

### 방법2 : Compound Pattern으로 조합하는 방식

```js
const Face = ({ children }) => {
  return <>{children}</>;
};

const Eyes = () => <>{'...'}</>;
const Nose = () => <>{'...'}</>;

Face.Eyes = Eyes;
Face.Nose = Nose;
```

```jsx
const AngryFace = () => (
  <Face>
    <Face.Eyes className="scale-x-50" />
    <Face.Nose className="bg-red" />
    {'...'}
  </Face>
);
```



# 스토리북을 활용하여 나아진 사례3 : 독립해서 그려보니 의도와 다르게 그려진 경우

## 첫번째 예

컴포넌트가 다른 컴포넌트에 의존성을 가졌을 경우, 독립해서 그려보니 예상과 다르게 그려지는 경우가 있다.

부모의 스타일에 의존하는 경우가 그렇다고 볼 수 있다.

```jsx
<div className="relative h-50 w-100">
  <Thumbnail />
</div>

// Thumbnail.tsx
const Thumbnail = () => (
  <div className="absolute right-0 h-50 w-50">
    <img />
  </div>
);
```

이런 스타일을 가진 컴포넌트의 경우 화면에서 사라지고 잘 찾아보면 오른쪽 구석에서 찾을 수 있다. 이런 경우 부모 컴포넌트와 같이 스토리를 만든다.

<br />

## 두번째 예

앱에서 ChapterTitle은 굉장히 자연스럽게 쓰이고 있다. 컴포넌트 상단에 여백이 적합한 문맥에 배치되어 있기 때문이다. 하지만, 스토리북에 그려보니 어색하게 그려진다.

```jsx
const ChapterTitle = () => {
  <div className="pt-34">
    <InnerContent />
  </div>;
};
```

<br />

아래와 같이 ChapterTitle 자체는 여백을 갖지 않게해서 스토리북에서 원하는데로 그릴 수 있다. 이렇게 하면 자연스럽게 다른 곳에서 재사용성도 좋아진다.

```jsx
const ChapterTitle = () => <InnerContent />;

<div className="pt-34">
  <ChapterTitle />
</div>;
```

<br />

## 세번째 예

앱에서 `useModal`을 실행하면 전역 환경에 선언된 모달을 띄워주는 기능을 잘 수행한다. 하지만, 스토리북의 독립된 환경에서 실행하면 useModal을 처리하지 못하고 에러를 반환한다. 환경에 의존성을 갖고 있는 경우다. 

```jsx
const Component = () => {
  const { openModal } = useModal(); // 전역에 선언된 모달을 띄워준다.
  <button onClick={() => openModal()} />;
};
```

<br />

다음과 같이 환경에 의존하지 않도록 커스텀훅을 사용하지 않고 클릭했을 때 실행할 함수를 전달받으면

스토리북에서 어떤 버튼을 클릭했을 때 어떤 이벤트가 발생할지 예측할 수 있다.

결과적으로 스토리북으로 옮기는 것이 가능해진다.

```jsx
const Component = ({ onOpenModal }) => <button onClick={() => onOpenModal()} />;
```

> 스토리북에서 버튼을 클릭하면 onOpenModal 버튼을 클릭할 때마다 Actions 탭에 추가되는 것을 볼 수 있다.



