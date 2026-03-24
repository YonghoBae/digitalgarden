---
{"dg-publish":true,"permalink":"/02 - Knowledge/AI/Agent/Agent Tools/","tags":["type/study","context/academic","theme/ai","status/in-progress"]}
---

# Agent Tools
> 에이전트가 외부 세계와 연결되는 3가지 도구

## 도구가 필요한 이유

- LLM은 학습 데이터의 **정적인 스냅샷**에 의존 → 실시간 정보 접근 불가
- 텍스트 생성은 가능하지만 **실제 행동**(검색, 코드 실행, DB 조회)은 불가
- 도구(Tools)는 에이전트가 **외부 시스템과 상호작용**하는 수단

> [!info] Google Agents 백서의 3가지 도구 분류
> 1. **Extensions** — 에이전트가 직접 API를 호출
> 2. **Functions** — 모델이 호출 명세를 생성, 실제 실행은 클라이언트가 담당
> 3. **Data Stores** — 벡터 DB 등을 통한 지식 검색 (RAG)

---

## Extensions

- 에이전트(서버 측)가 **직접 외부 API를 호출**하는 방식
- 에이전트가 API 명세를 학습하여 어떤 도구를 언제 쓸지 스스로 결정
- 실행 흐름: `에이전트 → API 호출 → 결과 수신 → 추론 반영`

**특징:**
- 에이전트가 실행 주체 (서버 사이드)
- 즉각적인 결과 반영 가능
- 보안/인증 처리가 에이전트 레이어에서 이루어짐

**예시:** Google Search API, Maps API, 날씨 API 직접 호출

---

## Functions (Function Calling)

- 모델이 **호출해야 할 함수 명세(이름, 파라미터)를 생성**하고, 실제 실행은 **클라이언트(애플리케이션)가 담당**
- 모델은 함수를 직접 실행하지 않음 → 실행 제어권이 개발자에게 있음
- 실행 흐름: `모델 → 함수 명세 생성 → 클라이언트 → 함수 실행 → 결과 반환 → 모델`

**특징:**
- 클라이언트 사이드 실행 (개발자가 실행 로직 제어)
- 보안이 중요한 작업(DB 쓰기, 결제)에 적합
- 실행 전 사람 또는 시스템의 승인 단계 삽입 가능

**예시:** 주문 처리, DB 업데이트, 이메일 발송 함수 호출

### Extensions vs Functions 차이

| 구분 | Extensions | Functions |
|------|-----------|-----------|
| **실행 주체** | 에이전트 (서버) | 클라이언트 (앱) |
| **실행 시점** | 즉시 | 클라이언트가 결정 |
| **제어권** | 에이전트 | 개발자 |
| **적합한 경우** | 읽기, 검색, 조회 | 쓰기, 결제, 민감한 작업 |

---

## Data Stores

- 에이전트에게 **외부 지식 베이스**를 제공하는 도구
- 벡터 데이터베이스(Vector DB), 문서 저장소 등을 통해 관련 정보를 검색
- **RAG (Retrieval-Augmented Generation)** 패턴의 핵심 컴포넌트

**지원하는 데이터 형태:**
- 웹사이트, PDF, 문서 (비정형 텍스트)
- 구조화 데이터 (SQL DB, Spanner 등)
- 이미지, 동영상, 오디오

### RAG 처리 흐름

```mermaid
flowchart LR
    Query([사용자 쿼리]) --> Embed["임베딩 생성\n(Embedding Model)"]
    Embed --> Search["유사도 검색\n(Vector DB)"]
    Search --> Retrieve["관련 문서 청크\n상위 K개 추출"]
    Retrieve --> Context["컨텍스트 구성\n(쿼리 + 문서)"]
    Context --> LLM["LLM 추론\n(답변 생성)"]
    LLM --> Answer([최종 응답])

    style Query fill:#24283b,stroke:#7dcfff,stroke-width:2px,color:#7dcfff
    style Answer fill:#24283b,stroke:#9ece6a,stroke-width:2px,color:#9ece6a
    style Embed fill:#24283b,stroke:#bb9af7,stroke-width:2px,color:#bb9af7
    style Search fill:#24283b,stroke:#7aa2f7,stroke-width:2px,color:#7aa2f7
    style Retrieve fill:#24283b,stroke:#7aa2f7,stroke-width:2px,color:#7aa2f7
    style Context fill:#24283b,stroke:#e0af68,stroke-width:2px,color:#e0af68
    style LLM fill:#24283b,stroke:#bb9af7,stroke-width:3px,color:#bb9af7
```

> [!TIP]
> RAG의 핵심: LLM의 고정된 학습 데이터 한계를 극복하여 **최신·도메인 특화 지식**을 동적으로 제공

자세한 내용: [[02 - Knowledge/AI/벡터 데이터베이스\|벡터 데이터베이스]]

---

## 도구 비교 요약

| 구분 | Extensions | Functions | Data Stores |
|------|-----------|-----------|-------------|
| **역할** | 외부 API 직접 호출 | 함수 명세 생성 | 지식 검색 (RAG) |
| **실행 위치** | 에이전트 (서버) | 클라이언트 (앱) | 에이전트 (서버) |
| **데이터 방향** | 에이전트 → 외부 | 외부 → 에이전트 (제어 위임) | 외부 → 에이전트 (읽기) |
| **주요 용도** | 실시간 조회, 액션 | 민감한 작업, 승인 필요 작업 | 최신 정보, 도메인 지식 |

---

## 모델 성능 향상 방법

> Google Agents 백서에서 Tools 챕터에 포함된 내용 — 도구와 함께 에이전트의 행동 품질을 결정하는 요소로 다뤄진다.

에이전트의 핵심인 LLM 자체의 성능을 높이는 3가지 접근법.

### In-context Learning

- 추가 학습 없이 **프롬프트에 예시·지시사항을 포함**하여 모델 행동을 조정
- 구현 비용: 낮음 / 지속성: 세션 내 한정

**방법:**
- **Zero-shot**: 예시 없이 지시사항만 제공
- **Few-shot**: 입출력 예시 몇 개를 프롬프트에 포함

### Retrieval-based In-context Learning

- **RAG**를 활용하여 동적으로 관련 예시·지식을 프롬프트에 주입
- 정적인 few-shot의 한계를 극복: 쿼리에 맞는 예시를 실시간 검색
- 구현 비용: 중간 / 지속성: 검색 결과에 의존

> [!example]
> "고객 불만 처리" 쿼리 → 유사 사례 Top-3 검색 → 프롬프트에 포함 → 더 정확한 응답 생성

### Fine-tuning

- 도메인 특화 데이터로 **모델 가중치를 직접 업데이트**
- 구현 비용: 높음 / 지속성: 영구적 (모델에 내재화)
- 반복적으로 필요한 특정 행동 패턴을 학습시킬 때 적합

| 방법 | 비용 | 지속성 | 적합한 상황 |
|------|------|--------|------------|
| **In-context** | 낮음 | 세션 한정 | 빠른 프로토타입, 간단한 조정 |
| **Retrieval-based** | 중간 | 동적 | 최신 정보, 대규모 지식 베이스 |
| **Fine-tuning** | 높음 | 영구 | 도메인 특화, 반복 패턴 학습 |

---

## 스스로 묻기
- Extensions와 Functions의 실행 주체 차이를 설명할 수 있는가?
- RAG가 In-context Learning과 어떻게 연결되는가?

## 참고 자료
- Google, "Agents" (February 2025) — Julia Wiesinger et al.

[[02 - Knowledge/AI/Agent/_Agent\|_Agent]] | [[02 - Knowledge/AI/Agent/AI Agent\|AI Agent]] | [[02 - Knowledge/AI/Agent/Agent Architecture\|Agent Architecture]]
