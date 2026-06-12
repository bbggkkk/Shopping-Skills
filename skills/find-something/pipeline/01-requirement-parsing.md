# Step 1: 요구사항 분석

## 개요

사용자로부터 요청을 받아 찾고자 하는 대상의 유형을 분류하고, 검색에 필요한 키워드와 검증 기준을 추출합니다.

## 입력

사용자로부터 다음 정보를 수집합니다:

| 항목 | 필수 | 설명 |
|------|------|------|
| **검색어/질문** | O | 찾고자 하는 대상이나 궁금한 내용 |
| **대상 유형** | X | 상품, 문서, 코드, 인물, 장소, 뉴스, 일반 |
| **세부 조건** | X | 추가 필터 조건 (날짜, 언어, 지역 등) |
| **기대 범위** | X | 결과의 깊이 (간략/상세/심층) |

## 처리 과정

### 1단계: 대상 유형 분류

사용자 입력에서 찾고자 하는 대상의 유형을 자동 분류합니다:

| 유형 | 키워드 예시 | 검색 전략 |
|------|-------------|-----------|
| **상품** | 상품, 제품, 가격, 구매, 쇼핑 | 웹 + 쇼핑몰 |
| **문서/정보** | 문서, 자료, 정보, 방법, 뜻, 개념 | 웹 + 문서 |
| **코드** | 코드, 함수, 라이브러리, API, 에러 | 코드 검색 + 웹 |
| **인물** | 인물, 사람, 프로필, 연예인, CEO | 웹 + 뉴스 |
| **장소** | 장소, 위치, 여행, 맛집, 카페 | 웹 + 이미지 |
| **뉴스/이슈** | 뉴스, 소식, 이슈, 최신, 트렌드 | 뉴스 + 웹 |
| **일반** | 분류 불명 | 웹 검색 |

### 2단계: 검색 키워드 생성

추출된 정보를 기반으로 검색 키워드를 생성합니다:

| 키워드 유형 | 형식 | 예시 |
|-------------|------|------|
| **기본 검색** | [핵심어] | "양자 컴퓨터 상용화" |
| **상세 검색** | [핵심어] [세부 조건] | "양자 컴퓨터 상용화 2024 IBM" |
| **대안 검색** | [핵심어] [관련어] | "양자 컴퓨터 vs 슈퍼컴퓨터 차이" |

### 3단계: 검증 기준 설정

유형에 따라 검증 기준을 자동 설정합니다:

```yaml
validation_criteria:
  document:
    accuracy_check: true
    recency_check: true
    source_authority: true
    cross_reference: true

  person:
    accuracy_check: true
    recency_check: true
    source_authority: true
    cross_reference: true

  news:
    accuracy_check: true
    recency_check: true
    max_age_days: 7
    source_authority: true
    cross_reference: true

  general:
    accuracy_check: true
    recency_check: false
    source_authority: false
    cross_reference: false
```

## 출력

```json
{
  "requirement_id": "req_001",
  "original_input": "양자 컴퓨터 상용화 현황과 주요 기업 동향",
  "parsed": {
    "target_type": "document",
    "core_topic": "양자 컴퓨터 상용화",
    "sub_topics": ["현황", "기업 동향"],
    "conditions": {
      "recency": "최신",
      "depth": "상세"
    },
    "language": "ko"
  },
  "search_keywords": {
    "basic": ["양자 컴퓨터 상용화"],
    "detailed": ["양자 컴퓨터 상용화 현황 기업"],
    "alternative": ["양자 컴퓨터 시장 동향", "양자 컴퓨터 IBM 구글"]
  },
  "validation_criteria": {
    "accuracy_check": true,
    "recency_check": true,
    "source_authority": true,
    "cross_reference": true
  }
}
```

## 규칙

1. **최소 1개 키워드** — 검색어는 반드시 1개 이상 추출
2. **유형 자동 분류** — 명시적이지 않은 경우 키워드로 추론
3. **유형별 전략 연결** — 분류된 유형에 따라 2단계 검색 전략 결정
4. **검증 기준 기본값** — 유형 미지정 시 일반(general) 기준 적용
