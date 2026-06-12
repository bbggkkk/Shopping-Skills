# Step 3: 결과 수집

## 개요

각 검색 소스에서 수집한 결과를 통합하고, 중복을 제거하며, 기본 필터링을 수행합니다.

## 처리 과정

### 1단계: 결과 통합

여러 검색 소스의 결과를 하나의 목록으로 통합합니다:

```
웹 검색 결과 (5건)
+ 쇼핑몰 검색 결과 (8건)
+ 가격 비교 결과 (3건)
= 통합 결과 (16건)
```

### 2단계: 중복 제거

동일한 상품을 여러 소스에서 발견한 경우 하나로 통합합니다.

#### 중복 기준

| 기준 | 방법 |
|------|------|
| **상품명 유사도** | 텍스트 유사도 80% 이상 |
| **모델번호 일치** | 동일 모델번호 |
| **URL 중복** | 동일 URL |
| **가격+판매처** | 동일 가격의 동일 판매처 |

#### 통합 규칙

```json
{
  "product": "원본 상품 정보",
  "sources": [
    {"source": "web", "url": "..."},
    {"source": "coupang", "url": "..."}
  ],
  "best_price": 1450000,
  "price_range": {"min": 1450000, "max": 1600000}
}
```

### 3단계: 신뢰도 평가

각 상품의 신뢰도를 평가합니다:

| 요소 | 가중치 | 평가 기준 |
|------|--------|-----------|
| **소스 수** | 30% | 여러 소스에서 확인된 상품 |
| **판매처 신뢰도** | 25% | 대형 쇼핑몰, 공식 판매처 |
| **리뷰/평점** | 20% | 평점 4.0 이상, 리뷰 10건 이상 |
| **가격 정합성** | 15% | 시장 평균가 대비 정상 범위 |
| **정보 완성도** | 10% | 상세 스펙, 이미지 등 정보 풍부 |

#### 신뢰도 점수 계산

```
trust_score = (
  source_count_score * 0.3 +
  seller_trust_score * 0.25 +
  review_score * 0.2 +
  price_consistency_score * 0.15 +
  information_completeness_score * 0.1
)
```

### 4단계: 기본 필터링

다음 기준으로 기본 필터링을 수행합니다:

| 필터 | 기준 | 처리 |
|------|------|------|
| **가격 범위** | 사용자 예산 범위 밖 | 제외 |
| **재고 상태** | 품절/재고 없음 | 별도 표시 |
| **판매처** | 비신뢰 판매처 | 신뢰도 낮춤 |
| **정보 부족** | 상세 정보 없음 | 신뢰도 낮춤 |

## 출력

```json
{
  "collection_id": "col_001",
  "search_id": "search_001",
  "products": [
    {
      "product_id": "prod_001",
      "name": "삼성 갤럭시북 프로 360 15인치 i7 16GB",
      "brand": "삼성",
      "model_number": "NT950QDX-G516",
      "category": "노트북",
      "price": {
        "min": 1450000,
        "max": 1600000,
        "average": 1520000,
        "currency": "KRW"
      },
      "sources": [
        {
          "source": "coupang",
          "url": "https://coupang.com/...",
          "price": 1480000,
          "stock": "in_stock",
          "seller": "삼성 공식스토어"
        },
        {
          "source": "naver",
          "url": "https://shopping.naver.com/...",
          "price": 1450000,
          "stock": "in_stock",
          "seller": "삼성전자 공식"
        }
      ],
      "trust_score": 0.85,
      "rating": 4.5,
      "review_count": 234,
      "specs": {
        "screen_size": "15.6인치",
        "cpu": "인텔 코어 i7-1360P",
        "ram": "16GB",
        "storage": "512GB SSD",
        "weight": "1.71kg"
      }
    }
  ],
  "summary": {
    "total_found": 15,
    "after_dedup": 8,
    "after_filter": 6,
    "price_range": {"min": 1400000, "max": 1800000}
  },
  "collection_timestamp": "2024-01-15T10:35:00Z"
}
```

## 규칙

1. **중복 제거 우선** — 통합 전 중복 제거 수행
2. **신뢰도 기반 정렬** — 높은 신뢰도 순으로 정렬
3. **가격 정보 보존** — 통합 시 모든 가격 정보 보존
4. **출처 명시** — 각 상품의 출처 정보 반드시 포함
