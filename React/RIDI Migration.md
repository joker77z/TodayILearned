

emotion사용



react-test-renderer
unit test 뿐만 아니라 component test까지하니까 불편한 상황 발생.
element접속할 때 에러를 발생시켜서 불안전하다.

React Testing Library로 현재는 사용 중이다.



feature, refactoring 번갈아가면서 작업하고 있다.



디렉토리 관리

Next.js 기반

components : pages에서 사용되는 컴포넌트 들이 있다. 도메인 별로 나뉘어져 있다. 두 개 이상 도메인에서 사용되는 공통 컴포넌트는 common에 넣어놨다. rtk를 사용 중이라 slice를 사용하기 위한 features디렉토리가 존재한다.(rtk를 사용해보지 못해서 현재는 무슨 의미인지 모르겠다. ) 어쨌든 도메인별로 나눈 이유는 각 디렉토리 별로 테스트 코드를 작성하기 위함이다. 모든 컴포넌트는 test를 가지게 된다. 스타일 파일도 Search.styles.ts 같이 따로 구별하고 있다. 화면, 비즈니스 로직을 component에 녹이고 style은 구분하기 위해 분리했다고 한다.

공통으로 사용되는 hooks

api통신을 위해

models와 server로 나뉘었다.

한 곳에서만 사용되는 hook은?
 공통 hook 디렉토리가 아니라 해당 도메인 디렉토링 ㅔ위치한다.
예를 들어 pages > 회원관련 도메인을 뜻하는 account에 signup 디렉토리 내에 hooks를 만들어서 컨트롤 한다.

pages



# 참고

[리디_React 컴포넌트 설계 방법 공개! 웹 코드 리팩토링 프로젝트](https://www.youtube.com/watch?v=6BG6O5F5dIs)

