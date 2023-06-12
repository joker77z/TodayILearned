# Project Organization and File Colocation

routing foler와 file convention을 제외하고 Next.js는 프로젝트 파일을 어떻게 구성하고 조합해야 하는지에 대한 의견이 없다.  

이 페이지는 프로젝트를 구성하는데 사용할 수 있는 기본 동작과 기능을 공유한다.

---

## Safe colocation by default

`app`디렉토리안에서 중첩된 폴더 계층은 route 구조를 정의한다.

각 폴더는 URL 경로에서 각 해당 부분에 매핑된 route segment를 나타낸다.  

그러나 폴더를 통해 경로 구조가 정의되더라도 `page.js`또는 `route.js`파일이 경로 세그먼트에 추가될 때까지 경로는 공개적으로 액세스 할 수 없다.  

그리고, 경로를 공개적으로 액세스할 수 있는 경우에도 `page.js` 또는 `route.js`에서 반환한 내용만 클라이언트로 전송된다. 이 의미는 프로젝트 파일을 실수로 라우팅 할 수가 없이 안전하게 배치할 수 있음을 의미한다.

![A diagram showing colocated project files are not routable even when a segment contains a page.js or route.js file.](../../../images/project-organization-colocation.png)

> **Good To Know**  
>
> - 이것은 `pages`내에 있는 모든 파일이 route로 고려되는 `pages`디렉토리랑 다르다. 
> - `app`내에서 프로젝트 파일을 배치할 수 있지만 꼭 그럴필요는 없다. 만약 `app`디렉토리 밖에 두는 것을 선호하면 그렇게 할 수 있다.

---

## Project organization features

Next.js는 프로젝트를 구성하는데 몇 가지 도움을 준다.

