# 2장 : 간단한 프로토콜 Http

## 리퀘스트와 리스폰스를 교환하여 성립

Http는 클라이언트로 요청(request)을 하면, 결과가 응답(response)로 되돌아온다. 즉, 반드시 클라이언트 측으로부터 통신이 시작된다.
서버에 요구하는 `GET`같은 종류를 메소드라고 부르고, 요구 대상 리소스를 나타내고 있는 주소를 `리퀘스트 URI`라고 한다.

<br />

### 리퀘스트 메세지

리퀘스트 메세지는 다음과 같이 구성되어 있다.

- 메소드
- URI
- 프로토콜 버전
- 리퀘스트 헤더
- 엔티티

```http
// 메소드, URI, 프로토콜 버전
POST /form/entry HTTP/1.1
```

```http
// 리퀘스트 헤더
Host: hacker.jp
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 16
```

```http
// 엔티티
name=ueno&age=37
```

<br />

### 리스폰스 메세지

리퀘스트 내용을 처리한 결과를 리스폰스로 클라이언트에 돌려준다.

```http
// 프로토콜 버전, 상태코드, 상태코드 설명
HTTP /1.1 200 OK
```

```http
// 헤더 영역
Date: Tue, 10 Jul 2012 06:50:15 GMT
Content-Length: 362
Content-Type: text/html

<html> // 바디 영역
...
```



## HTTP는 상태를 유지하지 않는 프로토콜

HTTP는 상태를 계속 유지하지 않는 **스테이트리스 프로토콜이다.**
HTTP프로토콜 레벨에서 이전에 보냈던 리퀘스트, 리스폰스에 대해 기억하지 않는다.
이는 매우 **빠르고 확실하게 처리하는 범위성을 확보하기 위해 간단하게 설계**되었다.

그러나, 웹이 진화함에 따라 스테이트리스 특성만으로 처리하기 어려워졌다. **다른 페이지로 이동할 때 로그인 상태를 유지할 필요가 있었다.**
**상태를 계속 유지하고 싶은 요구에 부응하기 위해, 쿠키라는 기술이 도입되었다.** 이를 통해, HTTP를 이용한 통신에서도 상태를 유지할 수 있어졌다.



## 리퀘스트 URI로 리소스를 식별

HTTP는 URI를 사용해서 리소스를 지정한다.

리퀘스트 URI를 지정하는 방법은 아래와 같다.

- 모든 URI를 리퀘스트URI에 포함.

  ```http
  GET http://hackr.jp/index.html HTTP/1.1
  ```

- 헤더의 Host필드에 네트워크 로케이션을 포함.

  ```http
  GET /index.html HTTP/1.1
  Host: hackr.jp
  ```

<br />

## 메소드 종류

- GET: 리퀘스트URI에 식별된 리소스를 요청한다.
  queryString을 이용하여 파라미터들을 요청할 수 있다.
- POST: 엔티티를 전송할 때 사용한다. GET도 전송할 수 있지만, 일반적으로 POST를 사용한다.
  POST요청은 GET과 달리 body를 이용하여 제한없이 데이터를 전달할 수 있다.
  내용이 GET에 비해 감춰져있어서 보안에 좋다고 생각할 수 있지만, 개발자 도구를 통해 확인할 수 있어서 민감한 데이터의 경우 반드시 암호화를 해서 전송해야 한다.

- PUT은 파일 전송, HEAD는 메세지 헤더만 취득, DELETE 삭제, PUT은 파일 전송, OPTIONS는 리퀘스트URI로 지정한 리소스가 제공하고 있는 메소드 조사, TRACE 경로조사, CONNECT는 프록시에 터널링 요구, 이런 메소드들이 있지만 보안상 사용하지 않는다고 한다.

  > RESTful한 설계에 따르면 put이나 patch는 수정, delete는 삭제를 담당해야 하는데 왜 보안상에 위험하다고 하는걸까? MDN에 따르면, get은 서버에 있는 데이터를 읽기만 하는 반면 put이나 delete는 데이터의 변경을 하기 때문에 위험하다고 하는 것 같다. 서버관리자가 수정이나 삭제에 대한 메소드를 인지하고 있고 함께 설계를 했을 때 안전하게 가능한 것 같다.



> **GET과 POST의 차이점에 대해 더 알아보자.**
>
> |      | Cache | Data Length Limit |   When Use   |
> | :--: | :---: | :---------------: | :----------: |
> | GET  |   O   |         O         | Data Request |
> | POST |   X   |         X         | Data Create  |
>
> GET은 캐싱이 된다. 캐싱에 대한 컨트롤은 Cache-control을 통해 서버에서 할 수 있다. URL을 통해 데이터를 요청하기 때문에 2083자로 길이 제한이 있다.
> POST는 캐싱이 되지 않는다. 항상 서버에 변화를 요구하는 메소드다. HTTP Body에 데이터를 담아 보내서 길이의 제한이 없다. 데이터를 생성, 삭제, 수정 다 할 수 있지만 RESTful하게 주로 생성을 담당한다.

<br />

## 지속 연결

HTTP초기 버전에서는 HTTP통신을 한 번 할때마다 TCP에 의해 연결, 종료를 할 필요가 있었다.
하지만, HTTP가 널리 보급됨에 따라 다량의 이미지를 포함한 문서가 늘어갔다. 이 때 여러번 리퀘스트를 하게 되었다.

HTTP1.1, HTTP1.0 일부에서는 이런 TCP연결 문제를 해결하고자 어느 한 쪽이 명시적으로 연결을 종료하지 않는 이상 TCP연결을 계속 유지하는 방안을 고안했다.

TCP연결을 계속 유지하게 되면 앞에 언급했던 여러번 리퀘스트를 요청하고 끊고 반복하면서 발생하는 오버헤드를 줄일 수 있다. 지속 연결은 여러 리퀘스트를 보낼 수 있는 파이프라인화를 가능하게 하였고, 리퀘스트를 병행으로 보낼 수 있게 되었다.

<br />

## 쿠키를 사용한 상태 관리

HTTP의 스테이트리스의 특징은 서버의 CPU나 메모리의 소비를 줄일 수 있다는 장점이 있다. 이 장점을 유지하면서 클라이언트는 상태를 유지하기 위해 쿠키를 사용하기 시작했다. 서버에서 `Set-cookie`를 이용해서 쿠키를 클라이언트에 전달하고, 다음 번에 클라이언트가 같은 서버로 `헤더의 Cookie`를 이용해 요청을 보낼 때 자동으로 쿠키 값을 넣어서 송신한다. **서버는 누구에게 무엇을 전달했는지를 기억했었기 때문에, 전에 왔던 녀석이라는 것을 인지할 수 있다.**

