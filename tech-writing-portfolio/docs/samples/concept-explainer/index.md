# API란?

## 개요

API(Application Programming Interface)는 애플리케이션(프로그램)끼리 서로 소통할 수 있도록 만든 규칙입니다.
웹, 앱, OS, HW 등 다양한 환경에서 사용되며, API를 통해 복잡한 내부 구조를 모두 파악하지 않고도 기능을 활용할 수 있습니다.

## API 사용 예시

- **Windows 파일 API**: 다른 앱에서 파일을 열고 저장할 수 있음
- **카메라 API**: 앱에서 스마트폰 카메라를 실행할 수 있음
- **Google 맵 API**: 앱에서 지도/위치 서비스를 사용할 수 있음

---

# REST API란?

## 개요

REST API는 API 중에서도 **웹(HTTP) 환경에서 REST 원칙을 따르는 방식**입니다. REST는 *Representational State Transfer*의 줄임말로, 웹 리소스를 다루는 규칙을 정의합니다. 

예를 들어, 사용자가 **스마트폰 앱에서 '주문 내역'을 탭하면**, 앱은 서버에 주문 내역에 대한 요청(request)을 보냅니다. 서버는 다시 응답(response)으로 주문 내역을 전달합니다. 이 때 앱과 서버가 같은 규칙으로 대화하는 방식이 바로 REST API입니다.  


## REST의 핵심 원칙

REST는 **Representational State Transfer**의 줄임말로, 다음과 같은 원칙을 따릅니다.

1. **리소스(Resource) 중심**  
   - 모든 데이터(사용자, 상품, 주문 등)는 ‘리소스’로 표현됩니다.  
   - 각 리소스는 **고유한 URL**로 구분됩니다.  
   - 예:  
     - `/users/1` → ID가 1인 사용자  
     - `/orders/25` → 주문 번호 25번  

2. **HTTP 메서드 활용**  
   - `GET` → 조회  
   - `POST` → 생성  
   - `PUT`/`PATCH` → 수정  
   - `DELETE` → 삭제  

3. **무상태성 (Stateless)**  
   - 서버는 이전 요청의 상태를 기억하지 않습니다.  
   - 매 요청은 독립적이며, 필요한 정보는 요청에 모두 담아야 합니다.  

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
