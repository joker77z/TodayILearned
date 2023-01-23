> 현재 우리 회사에서 사용하고 있는 레거시 코드들을 어떻게 하면 '잘' 리액트로 마이그레이션 할 수 있을지 고민이고 시간 날 때마다 시도 중이다. 우연히 리디에서도 마이그레이션을 시도하고 있는 유튜브를 보게 되서 간단하게 정리했다.



# testing Library

리디에서도 javascript, jQuery, 리액트 초기버전 등과 같이 레거시 코드들이 많이 있다고 한다.
그래서 React로 리팩토링을 할 때 정상적으로 동작하고 있다는 검증을 하기 위해 testing library를 사용하고 있다고 한다.

## react-test-renderer
unit test 뿐만 아니라 component test까지하니까 element에 접속할 때 에러를 발생시켜서 불안전했다고 한다.
`React Testing Library`로 현재는 사용 중이고 만족 중이라고 한다.



# 디렉토리 관리

Mirgation하고 있는 페이지들은 Next.js 기반이라고 한다.

## components 폴더, pages폴더

pages에서 사용되는 컴포넌트 들이 있다. 재밌는 점은 도메인 별로 나뉘어져 있다. 두 개 이상 도메인에서 사용되는 공통 컴포넌트는 common에 넣어놨다. rtk를 사용 중이라 slice를 사용하기 위한 features디렉토리가 존재한다.(rtk를 사용해보지 못해서 현재는 무슨 의미인지 모르겠다. ) 어쨌든 도메인별로 나눈 이유는 각 디렉토리 별로 테스트 코드를 작성하기 위함이다. 모든 컴포넌트는 test를 가지게 된다. 스타일 파일도 Search.styles.ts 같이 따로 구별하고 있다. 화면, 비즈니스 로직을 component에 녹이고 style은 구분하기 위해 분리했다고 한다.

## 기타 폴더들

- hook들이 모여있는 hooks폴더

>  **한 곳에서만 사용되는 hook은** 공통 hook 디렉토리가 아니라 해당 도메인 디렉토리에 위치한다.
> 예를 들어 회원관리 페이지에만 들어가는 hook이라면 pages > account > signup 디렉토리 내에 hooks 폴더를 만들어서 컨트롤 한다고 한다.

- api통신을 위해 models와 server폴더도 있다.



# 참고

[리디_React 컴포넌트 설계 방법 공개! 웹 코드 리팩토링 프로젝트](https://www.youtube.com/watch?v=6BG6O5F5dIs)

