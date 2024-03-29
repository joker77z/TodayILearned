> 최근에 두 도메인을 연동하면서 팝업을 사용할일이 많았다. 그러면서 window메서드를 많이 사용했다. 오늘 그 내용들을 정리하고자 한다. 오늘 내용들은 [모던 javascript 튜토리얼](https://ko.javascript.info/)을 기반으로 한다.



# 팝업과 윈도우 메서드

현대 웹 트렌드에서는 사실 더 이상 찾지 않는 기술은 맞다. (모달을 더 많이 사용하기 때문) 하지만, 이번 내가 겪은 경험처럼 두 도메인을 연동해야 할 때 꼭 한번 사용해야 할 일이 있을 수 있다. 알고 있으면 굉장히 유용하지만, 모르고 있다면 이것이 가능하기나한가? 의문을 가질 수 있다. 내가 그랬다..

이번에 제대로 정리해두면 구글, 네이버, 페이스북 같은 사이트와 계정 연동을 통해 로그인 기능을 구현하는 OAuth인가 방식, 휴대폰 인증, 전자 결제 등에서도 사용할 일이 있을 것이니 제대로 정리해두자.

어떤 특징에 의해서 사용될까?

- 팝업창은 별개의 윈도우 창으로 독립된 환경을 갖는다. 그래서 신뢰되지 않는 사이트에서 보안과 민감한 계정 인정, 결제 같은 곳에서 사용한다.
- 팝업 띄우는 과정이 간단하다.
- 팝업창에서 URL을 변경하거나 팝업창을 띄우게 한 부모인 `opener`에게 메시지를 전송할 수 있다.



## 1. Blocking

예전에는 악의적인 의도를 가진 사이트들이 광고성 팝업창을 계속 띄우는 경험을 한 적이 있을 것이다. 이제는 브라우저가 유저에 의해 발생한 팝업창이 아닌 경우에는 원천차단하고 있다. 유저에 의한 팝업창이란 onclick 같은 이벤트 리스너에 의해 클릭을 통한 팝업창 열기가 있을 수 있다.

```js
// 차단 : 브라우저에 의해 차단된다.
window.open('www.naver.com')

// 허용 : 유저에 의한 팝업창으로 허용된다.
button.onclick = () => {
  window.open('www.naver.com')
}
```

set

<br/>

그런데 setTimeout에 의해 열리는 팝업의 경우 크롬은 팝업창이 열리고, 파이어폭스는 기본적으로 차단하지만 2000ms로 설정하면 팝업창이 열린다. 2초보다 작거나 같은 지연시간은 유효한 요청이라고 판단하기 때문이다. 때문에, 브라우저 자체적으로 실시하는 차단 정책은 완벽하지 않다.

> 브라우저 차단 정책에 의해 막혔던적이 있는데 setTimeout을 사용해서 한번 개선해봐야겠다. (근데 브라우저에서 언제 setTimeout을 막을지도 모르니 테스트만 해보고 적용은 사용자가 팝업 차단을 허용해주는 지금 식으로 해야 할 것 같다.)

<br />

## 2. window.open

새로운 창을 열게하는 메서드는 다음과 같다.

```js
window.open(url, name, params);
```

`url` : 새로운 윈도우창에 로드할 url이다.

`name` : 새로운 윈도우창이 가지는 이름을 설정한다. 각 윈도우 창들은 **보이지는 않지만** 저마다 이름을 가지고 있다. 이 이름은 `window.name`으로 접근할 수 있다.

 이 name은 어떤 윈도우 창을 팝업창으로 사용할지 특정할 수 있다. 만약, **동일한 이름을 가지고 있는 윈도우가 이미 존재한다면, 입력된 URL 경로의 윈도우 창은 해당 이름을 가진 윈도우 창이 열리고, 그렇지 않은 경우엔 새 창**이 열린다.

`params` : 새로운 윈도우창의 설정값이다. 각 세팅은 `,`로 구분한다. 각 params 사이에는 공백이 없어야 한다. (eg. `width=200,height=100`)

params에서 설정할 수 있는 값들은 다음과 같은 것들이 있다.

- 위치

  - left/top : 화면(스크린)상에서 **어느 좌표에서 새 창이 뜰지 위치를 정할 수 있다.**
  - width/height : 새 창의 너비, 높이를 정한다. 이 때 **최소 너비/높이 제한이 있어서 보이지 않는 창은 만들 수 없다.**

- 윈도우 창 기능 관련
  아래 옵션들은 크롬에서 대부분 동작하지 않는다.
  
  - menubar : 새 윈도우 창의 브라우저 메뉴 출력 여부 결정
  - toolbar : 브라우저 네비게이션 바(뒤로가기, 앞으로가기) 출력 여부 결정 (크롬 안된다. 파폭, IE만 된다.)
  - location : 새 윈도우 창의 URL 필드 영역 출력 여부 결정(파이어폭스, IE는 숨길 수 없다.)
  - status : 상태바 출력여부 결정 (대부분 윈도우는 해당 UI를 출력하도록 강제한다.)
  - resizable : 새 윈도우 사이즈 조절 여부
  - scrollbars : 새 윈도우 스크롤 여부
  
  > 세번째 인수인 params가 없다면 기본으로 지정된 파라미터가 실행된다. 있는데 left/top이 없으면 마지막에 열렸던 윈도우 창과 근접하게 실행되고 width/height값이 없으면 마지막 윈도우 창과 같은 크기로 열린다.
  > 기타 자세한 확인은 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/open)에서 하자.
  



## 3. 기존 윈도우 창에서 팝업창으로 접근

`open` 메서드가 호출되고 나면 새로운 윈도우 창에 대한 참조를 반환한다.

이 참조를 이용해서 새 윈도우 창의 프로퍼티를 조작하거나, 위치를 변경하는 등 작업이 수행 가능하다.

```js
let newWindow = open('/', 'example', 'width=200,height=30');

// alert에서 새로 오픈된 윈도우 주소를 찾지 못한다.
alert(newWindow.location.href);

// onload 이후에야 새로 오픈된 팝업창을 참조할 수 있다.
newWindow.onlaod = function()
  let html = `<div style="font-size:30px">Welcome!</div>`;
  newWindow.document.body.insertAdjacentHTML('afterbegin', html);
};
```

위 코드에서 alert가 실행될 때는 아직 window를 참조할 수 없다. 참조하고자 할 때는 onload를 사용해야 참조해야 한다. (혹은 DOMContentLoaded)

> 새롭게 생성되는 윈도우 창은 동일 오리진 정책(same origin policy)가 적용된다. 따라서, 같은 프로토콜/도메인/포트를 가진 오리진은 자유롭게 접근이 가능하다. 하지만, 다른 오리진이라면 서로 통신이 불가능하다.



<br />

## 4. 팝업창에서 기존 윈도우 창 접근

열린 팝업창에서는 window.opener를 사용하여 기존 창에 접근할 수 있다. 그래서 부모창을 `오프너 윈도우`라고 부르기도 한다.

아래 코드를 실행하면 `오프너 윈도우` 내용을 변경한다.

```js
let newWin = window.open('about:blank', 'hello', 'width=200,height=200');

newWin.document.write(
  "<script>window.opener.document.body.innerHTML = 'Test'<\/script>"
);
```

> 이를 통해 동일 오리진 정책을 준수할 때 양방향 통신이 가능하다는 것을 알 수 있다.

<br />

## 5. 윈도우 종료

윈도우 창을 종료하기 위해서는 `window.close()`를, 종료되었는지 확인하기 위해서는 `window.closed` 프로퍼티를 확인하면 된다. 대부분 브라우저에서는 `window.open`을 통해 생성된 팝업창일 경우에만 `window.close()`가 된다. 이 때문에 팝업 윈도우 창은 종료가 가능하지만 메인 윈도우 창은 불가능하다.

`closed` 프로퍼티는 윈도우 창이 정상종료되었다면 `true`값을 갖는다. 

아래 코드는 로드함과 동시에 종료되는 코드다.

```js
let newWindow = open('/', 'example', 'width=300,height=300');

newWindow.onload = function() {
  newWindow.close();
  alert(newWindow.closed);	// true
};
```



<br />

## 6. 윈도우 창 이동 및 리사이징

윈도우 팝업 창이 열린 후 이동할 수 있는 방법이 있다. (악의적인 사용을 막기 위해 메인 윈도우 창에서는 이런 메서드를 차단한다.)

- 현재 위치에서 이동 : `win.moveBy(x, y)`
- 절대 좌표로 이동 : `win.moveTo(x, y)`
- 현재 사이즈를 기준으로 주어진 값으로 재조정 : `win.resizeTo(width, height)`

> - 윈도우 창의 사이즈 변화를 알 수 있는 onresize 이벤트 핸들러도 있다는 것을 함께 기억하자.
> - 최소화, 최대화는 OS레벨에서 지원하기 때문에 자바스크립트를 사용해서 컨트롤을 할 수 없다.

<br />

## 7. 윈도우 스크롤링

윈도우 팝업창 뿐만 아니라 현재 **메인 윈도우에서도 사용 가능**하다.

- 현재 위치에서 스크롤링 : `window.scrollBy(x, y)`
- 절대 좌표로 스크롤링 : `window.scrollTo(x, y)`
- 어떤 요소의 좌표로 스크롤링 : `element.scrollIntoVeiw(top)`
  어떤 요소의 상단에 위치하도록 이동. top or true를 쓰면 된다.
  반대로 false를 쓰면 bottom에 이동한다.

> - 스크롤링 역시 onscroll이벤트를 통해 브라우저에서 감지할 수 있다. 

<br />

## 8. 윈도우 focus/blur

window.focus() 나 window.blur() 메서드를 사용해서 윈도우 창을 focus/blur처리 할 수 있다. 그리고 사용자가 윈도우 창을 포커싱했을 때와 포커싱 하지 않았을 때를 감지할 수 있다.

window.focus()와 window.blur()는 윈도우 창 레벨에서 잘 사용되지 않는다. 이유는 악의를 가진 개발자에 의해 악용될 여지가 많았기 때문이다. 예를 들어 다음과 같은 코드를 보면 윈도우 창을 떠나려고 할 때 다시 포커싱 해버린다. 창에 가두려는 목적이다.

```js
window.onblur = () => window.focus();
```

때문에, 브라우저별로 상이하게 많은 제약사항들이 있다.

예를 들어, 모바일 브라우저에서는 window.focus()를 완전히 무시한다. 또한, 팝업창이 별개의 탭으로 열릴 때는 동작하지 않고, 새 창으로 열릴때만 동작한다.

그럼에도 불구하고, focus는 여전히 유용하여 완전히 차단하는 것은 어려움이 있다.

실제로, 팝업창이 열렸을 때 window.focus()를 쓰는 것은 좋은 생각이다. 새로 생성된 윈도우 창에 접근함을 명시적으로 알려주기 때문이다.

<br />

# 교차 윈도우 통신

동일 오리진 정책을 사용하지 않는다면 서로 다른 도메인에서 javascript로 컨트롤할 수 있게 되어 보안 문제로 이어질 수 있다.

## 1. 동일 오리진

IE를 제외하고 protocol, domain, port번호가 같다면 동일 오리진으로 판단한다. 동일 오리진이면 메인 윈도우 창에서 새로 열린 팝업창, 새로열린 창에 있는 iframe간에도 통신이 가능하다. 

동일 오리진 정책에 위배된다면, 접근이 불가능하다. 단 하나의 예외가 있는데 location은 가능하다. location을 이용해서 새로운팝업창을 redirect 시킬 수 있다. 그렇지만 location을 읽어와서 현재 사용자가 어디 위치에 있는지는 알 수 없다.



## 2. 서브 도메인 오리진

일반적으로 두 개의 URL이 서로 다른 도메인을 가지고 있으면 다른 도메인이다.

그런데, `second level domain`이 같은 경우가 있을 수 있다. 이럴 때는 동일 오리진과 마찬가지로 서로 공유할 수 있다.

- john.site.com
- peter.site.com

위 두개의 URL에서 second-level domain은 site.com이 되고 이럴 경우 동일 오리진으로 만들 수 있다.

```js
document.domain = 'site.com'
```

두 페이지에서 해당 코드를 모두 실행시킨다면, 두 도메인은 동일 오리진으로 취급된다.

> iframe 관련 내용은 한글로 번역해주신 곳을 참고하자. [모던JS심화 프레임과 윈도우](https://velog.io/@longroadhome/%EB%AA%A8%EB%8D%98JS-%EC%8B%AC%ED%99%94-%ED%94%84%EB%A0%88%EC%9E%84%EA%B3%BC-%EC%9C%88%EB%8F%84%EC%9A%B0)

<br />

## 3. 교차 윈도우 메세지

`postMessage`를 사용하면 어느 오리진이건 통신이 가능하다. 하지만, 무조건적으로 모든 코드를 허용하는 것은 심각한 보안 위협을 야기할 수 있어서 그에 걸맞는 코드를 작성해야 한다.

### 1. postMessage

```js
target.postMessage(data, targetOrigin)
```

- target

  - 부모 윈도우에서 자식 윈도우로 메세지를 전달할 경우

     부모 윈도우의 javascript 파일에서 window.open의 참조 target에게 메세지를 보낼 수 있다. 

  - 자식 윈도우에서 부모 윈도우로 메세지를 전달할 경우

    자식 윈도우의 javascript 파일에서 window.opener의 참조 target에게 메세지를 보낼 수 있다.

- data

  data의 경우 IE는 JSON.stringify를 사용하여 문자열로 직렬화해서 보내야 하고, 다른 브라우저의 경우 직렬화 알고리즘을 통해 직렬화를 지원하여 객체를 사용해도 된다.

- targetOrigin

  targetOrigin은 지정된 오리진과 일치한 url을 가져야 메세지를 받을 수 있다.

  

### 2. onMessage

메시지를 수신하기 위해서 타겟 윈도우는 message 이벤트 핸들러인 addEventListener의 message를 사용해야 한다. postMessage가 호출되었을 때만 동작한다.

```js
window.addEventListener('message', function(event) {
  if (event.origin !== 'http://javascript.info') {
    // 알려지지 않은 도메인일 경우 무시
    return;
  }
  
  alert('received: ', event.data);
  
  // 송신측에 바로 답장...
  // event.source.postMessage(...);
});
```

- data

  postMessage를 통해 전달된 데이터

- origin

  송신측 오리진

- source

  송신측 오리진의 참조. 이를 통해 source.postMessage(....)를 통해 송신 윈도우에게 답장이 가능하다.

  

