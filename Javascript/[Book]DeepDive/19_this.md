## this 키워드

"this는 **함수 호출 방식**에 의해 **동적**으로 결정된다."

this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. this는 어디서든 참조할 수 있다.

- 전역에서는 window
- 일반 함수 내부에서 window
- 메서드 내부에서는 메서드를 호출한 객체
- 생성자 함수 내부에서는 생성할 인스턴스

> 하지만, this는 자기 참조 변수로서 **객체 매서드 내부, 생성자 함수 내부**에서만 의미있다. strict mode에서 함수 내부 this는 undefined로 바인딩된다. 사용할 필요가 없기 때문이다.

<br />

## 함수 호출 방식과 this 바인딩

함수는 함수 객체가 생성되는 시점에 상위 스코프를 결정한다. 하지만, this는 함수 호출 시점에 결정된다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(this); // {value: 100, foo: f}
    
    function bar() {
      console.lob(this); // window. 중첩 함수도 일반함수로 호출했을 때는 window다.
    }
    bar();
  }
}

obj.foo();
```

> 콜백함수도 일반함수면 마찬가지다. 아래는 이런 상황에 사용 가능한 다양한 방법이다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    const that = this;
    
    setTimeout(function() {
      console.log(that.value);
    }, 100);
  }
}

obj.foo();
```

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(function() {
      console.log(this.value);
    }.bind(this), 100)
  }
};

obj.foo();
```

또는 화살표 함수의 this는 상위 스코프의 this를 가리킨다는 점을 이용할 수 있다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(() => console.log(this.value), 100); // 100
  }
}

obj.foo();
```





