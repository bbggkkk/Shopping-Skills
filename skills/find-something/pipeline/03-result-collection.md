# Step 3: 결과 수집

## 개요

각 검색 소스에서 수집한 결과를 통합하고, 중복을 제거하며, 기본 필터링을 수행합니다.

## 처리 과정

### 1단계: 결과 통합

여러 검색 소스의 결과를 하나의 목록으로 통합합니다:

```
웹 검색 결과 (8건)
+ 유형별 검색 결과 (5건)
= 통합 결과 (13건)
```

### 2단계: 중복 제거

동일한 내용을 여러 소스에서 발견한 경우 하나로 통합합니다.

#### 중복 기준

| 기준 | 방법 |
|------|------|
| **URL 일치** | 동일 URL |
| **제목 유사도** | 텍스트 유사도 80% 이상 |
| **내용 유사도** | 본문 요약 유사도 70% 이상 |

#### 통합 규칙

```json
{
  "result": "원본 결과 정보",
  "sources": [
    {"source": "google", "url": "..."},
    {"source": "naver", "url": "..."}
  ],
  "best_snippet": "가장 완전한 요약",
  "source_count": 2
}
```

### 3단계: 신뢰도 평가

각 결과의 신뢰도를 평가합니다:

| 요소 | 가중치 | 평가 기준 |
|------|--------|-----------|
| **소스 수** | 30% | 여러 소스에서 확인된 정보 |
| **출처 권위** | 25% | 공식/신뢰 도메인, 기관, 전문 매체 |
| **최신성** | 20% | 정보의 게시/수정일 |
| **정보 일관성** | 15% | 다른 결과와의 일관성 |
| **정보 완전성** | 10% | 충분한 상세 정보 포함 |

#### 신뢰도 점수 계산

```
trust_score = (
  source_count_score * 0.3 +
  source_authority_score * 0.25 +
  recency_score * 0.2 +
  consistency_score * 0.15 +
  information_completeness_score * 0.1
)
```

### 4단계: 기본 필터링

다음 기준으로 기본 필터링을 수행합니다:

| 필터 | 기준 | 처리 |
|------|------|------|
| **최소 신뢰도** | 신뢰도 0.4 미만 | 제외 (출처 불명) |
| **출처 없음** | 출처 URL 없음 | 제외 |
| **명백한 오정보** | 확인된 가짜 뉴스/정보 | 제외 |
| **블랙리스트 도메인** | 설정된 차단 도메인 | 제외 |

## 출력

```json
{
  "collection_id": "col_001",
  "search_id": "search_001",
  "target_type": "document",
  "results": [
    {
      "result_id": "res_001",
      "title": "양자 컴퓨터 상용화 현황 및 전망",
      "sources": [
        {
          "source": "google",
          "url": "https://example.com/quantum-commercialization",
          "domain": "example.com",
          "trust_level": "medium"
        },
        {
          "source": "naver",
          "url": "https://blog.naver.com/...",
          "domain": "blog.naver.com",
          "trust_level": "medium"
        }
      ],
      "trust_score": 0.82,
      "published_date": "2024-10-15",
      "snippet": "양자 컴퓨터 상용화가 2024년부터 본격화되면서 IBM, 구글 등의 주요 기업들이...",
      "relevance": 0.95
    }
  ],
  "summary": {
    "total_found": 13,
    "after_dedup": 9,
    "after_filter": 7,
    "trust_score_range": {"min": 0.45, "max": 0.92}
  },
  "collection_timestamp": "2024-10-20T09:05:00Z"
}
```

## 규칙

1. **중복 제거 우선** — 통합 전 중복 제거 수행
2. **신뢰도 기반 정렬** — 높은 신뢰도 순으로 정렬
3. **출처 명시** — 각 결과의 출처 정보 반드시 포함
4. **다중 소스 우대** — 여러 소스에서 확인된 결과 우선
