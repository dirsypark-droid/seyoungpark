# Commerce Public API v1

- Base URL: `https://tech-writer-syp.com/v1`

- Auth: `Authorization: Bearer <token>`

- Content-Type: `application/json; charset=utf-8`

- Idempotency: `Idempotency-Key: <uuid>` (POST 시 권장)

- Rate Limit: 응답 헤더 `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`

# 공통 규칙

- 시간은 **UTC ISO 8601**(`YYYY-MM-DDTHH:mm:ssZ`) 사용

- URL은 `format: uri` (이미지/링크 등)

- 한글 메시지 기본, `Accept-Language: en` 등으로 다국어(선택) 지원

- 모든 에러는 **표준 에러 객체**로 반환

# 에러 객체(표준)

```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "\"price\"는 정수여야 합니다.",
    "field": "price",
    "expected": { "type": "integer", "minimum": 0, "maximum": 1000000000 },
    "actual": { "type": "string" },
    "requestId": "req_7fd2d4f2",
    "locale": "ko-KR"
  }
}
```

| 필드      | 타입   |   필수   | 설명                                                                                                                                         |
| --------- | ------ | ------- | ------------------------------------------------------------------------------------------------------ |
| code      | string |   ✔    | 에러 코드 (`MISSING_PARAMETER`, `INVALID_PARAMETER`, `OUT_OF_RANGE`, `NOT_FOUND`, `DUPLICATE_ID`, `TOO_MANY_IDS`, `IDEMPOTENCY_CONFLICT` 등) |
| message   | string |   ✔    | 오류 메시지(요청 언어 우선)                                                                                                                  |
| field     | string |        | 문제가 된 필드명(가능한 경우)                                                                                                                |
| expected  | object |        | 기대 스키마 일부(타입/범위/패턴 등)                                                                                                          |
| actual    | object |        | 실측 타입/값 요약                                                                                                                            |
| requestId | string |        | 서버 로그 상호 참조용 식별자                                                                                                                 |
| locale    | string |        | 메시지 언어 코드(예: `ko-KR`, `en-US`)                                                                                                       |

---

# 상품 등록 API

새로운 상품을 등록합니다.

## Endpoint

`POST` /products

멱등성: 네트워크 재시도 안전을 위해 `Idempotency-Key` 헤더 사용을 권장합니다. 동일 키로 같은 페이로드를 재전송하면 단일 결과로 통합됩니다.

## Request

| 필드        | 타입    | 필수 | 제약                                                    | 설명                                         |
| ----------- | ------- | ---- | ------------------------------------------------------- | -------------------------------------------- |
| name        | string  | ✔    | 1–255, 정규식 `^[A-Za-z0-9 _\\-:\\.^@]+$` _(공백 허용)_ | 대표 상품명(검색/노출에 사용)                |
| price       | integer | ✔    | 0–1,000,000,000                                         | 판매가(원, **부가세 포함**)                  |
| stock       | integer | ✔    | 0–1,000,000,000                                         | 재고 수량(**0이면 품절**)                    |
| description | string  |      | 1–2,000                                                 | 상세 설명(주요 특징/용도/원산지/주의사항 등) |

### Example request

```bash

curl -X POST https://tech-writer-assignment.com/v1/products \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: 4b8e7c1a-7c7b-4e71-9d3c-7b2f1c92e7aa" \
  -d '{
    "name": "삼다수 500ml 20개입",
    "price": 15000,
    "stock": 100,
    "description": "제주 삼다수 500ml 생수 20개 묶음입니다."
  }'

```

## Response

### 201 Created(상품 등록 성공)

- 헤더: `Location: /v1/products/{id}`

```json
{
  "id": "prod_01H8PY3NR7X7V5GJH9QFZ3N5A7",
  "name": "삼다수 500ml 20개입",
  "price": 15000,
  "stock": 100,
  "createdAt": "2025-04-28T10:15:30Z"
}
```

### 400 Bad Request(검증 실패)

```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "\"price\"는 정수여야 합니다.",
    "field": "price",
    "expected": { "type": "integer", "minimum": 0, "maximum": 1000000000 },
    "actual": { "type": "string" },
    "requestId": "req_ab12cd34"
  }
}
```

### 409 Conflict(멱등성 충돌/중복 생성 방지)

```json
{
  "error": {
    "code": "IDEMPOTENCY_CONFLICT",
    "message": "동일한 요청이 이미 처리되었습니다.",
    "requestId": "req_9a1cbb12"
  }
}
```

---

# 상품 카테고리 조회 API

주어진 ID 목록에 해당하는 카테고리 정보와 주요 상품 정보를 조회합니다. 응답 크기 제어를 위해 요청당 ID 개수 제한을 둘 수 있습니다(예: 최대 10,000개).

## Endpoint

`POST` /categories/batchGet

## Request

| 필드   | 타입            | 필수 | 제약                                         | 설명                    |
| ------ | --------------- | ---- | -------------------------------------------- | ----------------------- |
| catIds | array\<integer> | ✔    | 각 값 1–9,999,999,999, **uniqueItems: true** | 조회할 카테고리 ID 목록 |

### Example request

```bash

curl -X POST https://tech-writer-assignment.com/v1/categories:batchGet \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "catIds": [82275455, 103718011]
  }'

```

## Response

### 200 OK(상품 조회 성공)

```json
{
  "data": {
    "list": [
      {
        "cat": {
          "catId": 82275455,
          "name": "들기름/참기름", // 카테고리명
          "brandName": "농부미학", // 없을 수 있음(null)
          "attributes": [
            { "key": "용량", "value": "350", "unit": "ml" },
            { "key": "개수", "value": "1", "unit": "개" }
          ],
          "bup": { "quantity": 100.0, "unit": "ml" }, // 단위 기준가 계산용
          "thumbnailUrl": null,
          "productItemGroupId": 82275455
        },
        "reviewStat": {
          "totalCount": 7,
          "averageRating": 4.3
        },
        "primaryItem": {
          "itemId": 9763867,
          "name": "들기름 350ml",
          "thumbnailUrl": "https://shopping.syp.io/...",
          "stock": {
            "stockId": 2878365,
            "price": 8900,
            "originPrice": 18900,
            "baseUnitPrice": 2542.857
          },
          "product": {
            "productId": 267091,
            "name": "저온압착 참·들기름 180/350ml",
            "thumbnailUrl": "https://shopping.syp.io/...",
            "deliveryFeeType": "CONDITIONALLY_FREE"
          },
          "productItemId": 9763867,
          "price": 8900,
          "originPrice": 18900
        }
      }
    ]
  },
  "requestId": "req_58f0af20"
}
```

### 200 Partial Success(부분 성공)

존재하는 ID만 `data.list`에 포함하고, 유효하지 않은 ID는 `errors[]`에 나열

```json
{
  "data": {
    "list": [
      /* 존재하는 항목들 */
    ]
  },
  "errors": [
    {
      "code": "NOT_FOUND",
      "message": "카테고리 ID가 존재하지 않습니다.",
      "id": 55555555
    }
  ],
  "requestId": "req_ba02c1e0"
}
```

400 Base Request(중복/검증 실패)

```json
{
  "error": {
    "code": "DUPLICATE_ID",
    "message": "catIds에 중복 값이 포함되어 있습니다.",
    "field": "catIds",
    "requestId": "req_0f0f0f0f"
  }
}
```

---

# 인증(Authorization)

- 모든 요청에 `Authorization: Bearer <token>` 필요

- 잘못된 토큰: `401 Unauthorized`

- 권한 부족: `403 Forbidden`

---

# 국제화(i18n)

- `Accept-Language` 요청 헤더로 오류 메시지 언어 결정(기본: 한국어)

- 예: `Accept-Language: en` → 영어 메시지 반환

---

# 페이징/성능(해당 시)

- 배치 조회 시 **ID 개수 제한**(예: 최대 10,000)

- 필요한 경우 필드 선택을 위한 `fields` 쿼리/바디 옵션 제공 고려(예: `fields=cat,primaryItem`)

---

# 변경 관리(버전/폐기)

- 경로 버전 사용: `/v1/...`

- 호환 깨지는 변경은 **메이저 버전**으로 승격(`/v2`)

- 폐기 예정 필드는 문서에 `Deprecated` 표기 및 **Sunset 날짜** 공지

---

# 검증 규칙 요약

- **name**: 1–255, 정규식 `^[A-Za-z0-9 _\-:\.^@]+$` (공백 허용), 허용문자 외 금지

- **price/stock**: 정수, 0–1,000,000,000 (0이면 품절)

- **description**: 1–2,000(선택)

- **thumbnailUrl**: `format: uri`, nullable

- **createdAt**: ISO 8601(UTC, `Z`)

---

# 실패 케이스 예시 모음

## 필수 누락

```json
{
  "error": {
    "code": "MISSING_PARAMETER",
    "message": "\"name\" 필드는 필수입니다.",
    "field": "name",
    "requestId": "req_11112222"
  }
}
```

## 범위 초과

```json
{
  "error": {
    "code": "OUT_OF_RANGE",
    "message": "\"price\"가 허용 범위를 초과했습니다.",
    "field": "price",
    "expected": { "minimum": 0, "maximum": 1000000000 },
    "actual": { "value": 2000000000 },
    "requestId": "req_33334444"
  }
}
```

## 패턴 불일치

```json
{
  "error": {
    "code": "INVALID_FORMAT",
    "message": "\"name\"이 허용되지 않는 문자를 포함합니다.",
    "field": "name",
    "expected": { "pattern": "^[A-Za-z0-9 _\\-:\\.^@]+$" },
    "requestId": "req_55556666"
  }
}
```
