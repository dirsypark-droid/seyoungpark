# API란?

![API](../../assets/api-gen.jpg)

## 개요

API(Application Programming Interface)는 애플리케이션(프로그램)끼리 서로 소통할 수 있도록 만든 규칙입니다.
웹, 앱, OS, HW 등 다양한 환경에서 사용되며, API를 통해 애플리케이션의 복잡한 내부 구조를 모두 파악하지 않고도 기능을 활용할 수 있습니다.

## API 사용 예시

- **Windows 파일 API**: 다른 앱에서 파일을 열고 저장
- **카메라 API**: 앱에서 스마트폰 카메라 실행
- **Google 맵 API**: 앱에서 지도/위치 서비스 사용

---

# REST API란?

![REST API](../../assets/rest-api.jpg)

## 개요

REST API는 API 중에서도 **웹(HTTP) 환경에서 REST 원칙을 따르는 방식**입니다. REST는 *Representational State Transfer*의 줄임말로, 웹 리소스를 다루는 규칙을 정의합니다. 

예를 들어, 사용자가 **스마트폰 앱에서 '주문 내역'을 탭하면**, 앱은 서버에 주문 내역에 대한 요청(request)을 보냅니다. 서버는 다시 응답(response)으로 주문 내역을 전달합니다. 이 때 앱과 서버가 같은 규칙으로 대화하는 방식이 바로 REST API입니다.

> **참고**: REST 원칙을 따르는 서비스 인터페이스를 **RESTful**한 아키텍처라고 부릅니다.


## REST의 핵심 원칙

REST는 다음과 같은 원칙을 따릅니다.

1. **리소스 중심**  
   - 모든 데이터(사용자, 상품, 주문 등)는 ‘리소스’로 표현됩니다.  
   - 각 리소스는 **고유한 URL**로 구분됩니다.
   - 리소스 표현은 JSON, XML 등 다양한 형태를 가질 수 있습니다.
   - 예:  
     - `/users/1` → ID가 1인 사용자  
     - `/orders/25` → 주문 번호 25번
    
요청 예시(GET /users/1)

```http

GET /users/1 HTTP/1.1
Host: api.example.com
Accept: application/json

```

응답 예시(JSON)
```json

{
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com"
}

```

2. **HTTP 메서드 활용**

   - `GET` → 조회(Create)

```http
GET /orders/25 HTTP/1.1
Host: api.example.com
```
   - `POST` → 생성(Read)
```http
POST /orders HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "productId": 123,
  "quantity": 2
}
```
   - `PUT`/`PATCH` → 수정(Update)
```http
PUT /orders/25 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "quantity": 3
}
```
   - `DELETE` → 삭제(Delete)
```http
DELETE /orders/25 HTTP/1.1
Host: api.example.com
```

2. **무상태성**  
   - 서버는 클라이언트의 상태(세션 등)를 저장하지 않습니다.  
   - 각 요청은 독립적이며, 필요한 정보는 요청에 모두 포함해야 합니다.

예: 요청 시 인증 토큰 함께 전송
```http
GET /users/1 HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

3. **클라이언트-서버 구조**
   - 클라이언트와 서버의 역할을 명확히 분리하여 확장성과 유지보수성을 향상합니다.
   - 클라이언트는 UI/사용자 경험 담당, 서버는 데이터 저장/처리를 담당합니다.

서버 응답(JSON) - 데이터 담당
```json
{
  "id": 1,
  "title": "REST API란?",
  "content": "리소스 중심 아키텍처 스타일입니다."
}
```

클라이언트(JavaScript에서 데이터 표시) - UI 담
```javascript
fetch("https://api.example.com/posts/1")
  .then(res => res.json())
  .then(data => {
    document.getElementById("title").textContent = data.title;
    document.getElementById("content").textContent = data.content;
  });
```
  
4. **캐시 가능성**
   - 서버 응답은 캐시 가능해야 하며, 캐시 가능 여부를 명시해야 하며, 이를 통해 성능을 개선하고 네트워크 트래픽 감소 효과를 얻습니다.
   - 예: `Cache-Control` 헤더로 응답이 캐시 가능한지, 유효기간은 언제까지인지 명시

서버 응답 헤더
```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=3600   # 1시간 동안 캐시 가능
```
응답 본문
```
{
  "id": 123,
  "name": "Laptop",
  "price": 1500
}
```

> **참고**: 캐시(Cache)란 한 번 가져운 데이터를 잠시 저장해 두었다가, 같은 요청이 또 들어오면 다시 서버를 거치지 않고 저장한 데이터를 재사용하는 것을 말합니다.

6. **계층화 시스템**
   - 서버는 여러 계층(로드밸런서, 프록시, 게이트웨이 등)으로 구성될 수 있으며, 클라이언트는 중간 계층의 존재를 알 필요가 없습니다.
   - 보안, 로드 분산, 정책 관리 등을 계층에서 처리할 수 있습니다.

클라이언트 요청
```http
GET /products/123 HTTP/1.1
Host: api.example.com
```
예시 경로
```pgsql
Client → Load Balancer → API Gateway → Application Server → Database
```

7. **코드 온 디맨드(선택적)**
   - 필요 시 서버가 클라이언트에 코드를 전송해 실행할 수 있습니다.
   - 예: 이메일 형식 검사, 비밀번호 규칙 검사 

유효성 검사 예시
```html

<input type="email" id="email" />
<script>
  function validateEmail() {
    const email = document.getElementById("email").value;
    if (!email.includes("@")) {
      alert("유효한 이메일을 입력하세요");
    }
  }
  document.getElementById("email").addEventListener("blur", validateEmail);
</script>

```

## REST API 사용 예시
- **GET /products**  
  → 모든 상품 목록을 가져오기  
- **POST /products**  
  → 새로운 상품 추가하기  
- **GET /products/123**  
  → ID 123번 상품 정보 조회하기  
- **DELETE /products/123**  
  → ID 123번 상품 삭제하기

## API vs. REST API

| 구분 | API | REST API |
|------|-----|-----------|
| 개념 범위 | 프로그램 간 소통을 위한 모든 규칙 | API 중 REST 원칙을 지키며 HTTP로 통신하는 방식 |
| 사용 환경 | OS, 라이브러리, 앱, 웹, 하드웨어 전반 | 주로 웹(HTTP 기반) |
| 예시 | 카메라 API, 결제 API, 구글 맵 API | `GET /users`, `POST /orders` |

---

# 참고 자료

- [AWS: API란 무엇인가?](https://aws.amazon.com/ko/what-is/api/)  
- [YouTube: APIs Explained in 6 Minutes](https://www.youtube.com/watch?v=hltLrjabkiY)  
- [MDN Web Docs: Introduction to APIs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)  
- [Google Cloud: What is an API?](https://cloud.google.com/learn/what-is-an-api)  
- [Microsoft Docs: What is an API?](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/api-design)  
- [REST API Tutorial](https://restfulapi.net/)  
