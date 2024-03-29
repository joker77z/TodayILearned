# 3장 : HTTP정보는 HTTP메세지에 있다.

HTTP로 데이터를 전송할 경우 그대로 전송할 수도 있지만, 전송할 때에 인코딩(변환)을 실시함으로써 전송 효율을 높일 수 있다. 단, 인코딩을 하려면 CPU등의 리소스는 더 소비하게 된다.

<br />

## 메시지 바디와 엔티티 바디의 차이

- 메시지(message)

  HTTP통신의 기본 단위. 옥텟 시퀀스(8비트)로 구성되고 통신을 통해서 전송된다.

- 엔티티

  메시지의 바디에 적재되는 실제 데이터다. 일반적으로 메시지 바디와 엔티티 바디는 일치하지만, **전송 인코딩**이 적용된 경우 다르다.
  http 메시지의 바디는 엔티티 바디를 운반하는 역할을 한다.

<br />

## 압축해서 보내는 콘텐츠 인코딩

HTTP에는 콘텐츠 인코딩이라고 불리는 기능이 구현되어 있다. 엔티티 정보를 유지한 채로 엔티티를 압축한다. 수신한 클라이언트 측에서 디코딩한다.

<br />

## 분해해서 보내는 청크 전송 코딩

HTTP통신에서는 요청했던 리소스 전부가 전송되지 않으면 브라우저에 표시되지 않는다.
사이즈가 큰 데이터를 전송하는 경우, **데이터를 분할하여 조금씩 표시할 수 있다.**
이렇게 엔티티 바디를 조금씩 분할하는 기능을 **청크 전송 인코딩**이라고 한다.

<br />

## 여러 데이터를 보내는 멀티파트

메일의 경우 본문 및 여러 첨부파일을 붙여 함께 보낼 수 있다. 이것은 MIME로 불리는 기능을 사용하고 있다. MIME는 이미지 등 바이너리 데이터를 아스키 코드로 인코딩하는 방법과 데이터 종류를 나타내는 방법 등을 규정하고 있다. MIME의 확장사양인 멀티파트를 사용하여 다양한 종류의 데이터를 수용하고 있다.

**HTTP도 멀티파트에 대응하고 있어서 하나의 메세지 바디 내부에 인티티 여러개를 포함할 수 있다.** 멀티파티에는 다음과 같은 것이 있다.

- multipart/form-data

  form으로부터 파일 업로드에 사용된다.

- multipart/byteranges

  상태 코드 206 리스폰스 메세지가 복수 범위 내용을 포함할 때 사용한다.

HTTP 메시지로 멀티파트를 사용할 때 `Content-type` 헤더 필드를 사용한다. 멀티파트를 사용할 때 각 엔티티의 선두에 `--`가 들어가고 마지막 엔티티 뒤에도 `--`가 들어간다.

```http
Content-Type: multipart/form-data; boundary=AaB03x

--AaB03x
Content-Disposition: form-data; name="field1"

Joe Blow
--AaB03x
Content-Disposition: form-data; name="pics"; filename="file1.txt"
Content-Type: text/plain

// file1.txt데이터
--AaB03x--
```

<br />

## 일부분만 받는 레인지 리퀘스트

예전에는 대용량 이미지, 데이터를 처리하기가 힘들었다. 그래서 중간에 커넥션이 끊겼을 때 다시 처음부터 다운로드받아야 했다. 이런 문제를 해결하기 위해서 resume이라는 기능이 필요하게 되었다.

이 기능을 실현하기 위해서 엔티티의 범위를 지정해서 다운로드 할 필요가 있다. 이렇게 범위를 지정하여 리퀘스트 하는 것을 **레인지 리퀘스트**라고 부른다.

```http
GET /tip.jpg HTTP/1.1
Host: www.usagidesion.jp
Range: bytes=5001-10000
```

```http
HTTP/1.1 206 Partial Content
Date: Fri, 13 Jul 2012 04:39:17 GMT
Content-Range: bytes 5001-10000/10000
Content-Length: 5000
Content-Type: image/jpeg
```

<br />

## 최적의 콘텐츠를 돌려주는 콘텐츠 네고시에이션

같은 내용(컨텐츠)이지만 여러 개 페이지를 지닌 웹 페이지가 있다. 예로, 서로 언어가 다른 페이지를 띄워주는 경우다.

이런 구조를 **콘텐츠 네고시에이션**이라고 부른다. 클라이언트와 서버가 제공하는 리소스 내용을 교섭하는 것이다.

콘텐츠 네고시에이션을 판단하는 기준으로 리퀘스트 헤더 필드에는 이런 것이 있다.

- Accept
- Accept-Charset
- Accept-Encoding
- Accept-Language
- Content-Language

콘텐츠 네고시에이션에는 다음과 같은 종류들이 있다.

- 서버 구동형 네고시에이션(Server-driven Negotiation) : 리퀘스트 헤더 필드 정보를 참고해서 자동으로 처리한다.
- 에이전트 구동형 네고시에이션(Agent-driven Negotiaition) : 클라이언트 측에서 콘텐츠 네고시에이션 하는 방식으로 유저가 수동으로 선택하거나, JS 등으로 자동으로 정하는 것이다. OS, 브라우저 종류, PC/스마트폰에 따라 웹 페이지를 자동으로 전환하는 것도 포함이다.
- 트랜스페어런트 네고시에이션(Transparent Negotiation) : 서버 구동형과 에이전트 구동형을 혼합한 것이다.

