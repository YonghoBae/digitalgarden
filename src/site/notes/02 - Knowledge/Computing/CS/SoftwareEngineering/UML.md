---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/SoftwareEngineering/UML/","tags":["type/study","context/studies","theme/cs","status/completed"]}
---

# UML (Unified Modeling Language)

> 구조적 모델링, 행위적 모델링, CRC 카드, Use Case

## 다이어그램 분류

| 분류 | 다이어그램 |
|------|-----------|
| **구조적 모델링** | 클래스, 객체, 컴포넌트, 배포 다이어그램 |
| **행위적 모델링** | 시퀀스, 커뮤니케이션, 상태머신, 활동, Use Case 다이어그램 |

---

## 구조적 모델링 (Structural Modeling)

- 시스템에서 사용되고 생성되는 객체의 **정적 관점** 표현
- **클래스 다이어그램**: 클래스, 속성, 연산, 관계 표현

### 클래스 간 관계

| 관계 | 표기 | 설명 |
|------|------|------|
| **연관(Association)** | ─── | 클래스 간 일반적 관계 |
| **집합(Aggregation)** | ◇─── | 전체-부분 관계 (약한 소유) |
| **합성(Composition)** | ◆─── | 전체-부분 관계 (강한 소유, 생명주기 공유) |
| **상속(Generalization)** | ───▷ | is-a 관계 |
| **의존(Dependency)** | - - ▷ | 한 클래스가 다른 클래스 사용 |
| **실현(Realization)** | - - ▷ | 인터페이스 구현 |

---

## 행위적 모델링 (Behavioral Modeling)

객체의 **동적 관점** 표현

### 시퀀스 다이어그램 (Sequence Diagram)
- **시간 흐름** 중심의 객체 상호작용
- Instance form vs Generic form
- 구성: Lifeline, Message, Activation Bar

### 커뮤니케이션 다이어그램 (Communication Diagram)
- **객체 간 메시지 흐름** 중심 (관계 네트워크 강조)

### 상태머신 다이어그램 (State Machine Diagram)
- 객체의 **상태 변화** 표현
- 상태, 전이, 이벤트, 가드 조건

### 활동 다이어그램 (Activity Diagram)
- **업무 프로세스 흐름** 표현
- 순서도와 유사, 병렬 처리 표현 가능

### Use Case 다이어그램
- 시스템과 사용자(Actor) 간의 상호작용
- 기능 요구사항 표현

---

## 객체 식별 방법

1. **Textual Analysis**: Use case 설명에서 명사 → 클래스, 동사 → 연산
2. **Common Object List**: 도메인 공통 객체 목록 참조
3. **Brainstorming**: 팀 아이디어 수집
4. **Patterns**: 공통 클래스 집합 재사용

---

## CRC 카드 (Class-Responsibility-Collaborator)

| 항목 | 내용 |
|------|------|
| **Class Name** | 클래스 이름 및 ID, 타입 |
| **Description** | 클래스 설명, 관련 Use Case |
| **Responsibilities** | 이 클래스가 해야 할 일 |
| **Collaborators** | 협력하는 다른 클래스 |
| **Attributes** | 속성 목록 |
| **Relationships** | 일반화, 집계 관계 |

- Use case 롤플레이를 통해 CRC 카드 검증

---

## Model Balancing

- 여러 UML 다이어그램이 서로 **일관성** 있게 맞춰져야 함
- 클래스 다이어그램의 클래스 ↔ 시퀀스 다이어그램의 Lifeline이 일치해야 함

## 관련 개념
- [[02 - Knowledge/Computing/CS/SoftwareEngineering/소프트웨어 설계\|소프트웨어 설계]] - 설계 원칙 (응집도, 결합도)
- [[02 - Knowledge/Computing/CS/SoftwareEngineering/GOF 패턴\|GOF 패턴]] - 패턴을 UML로 표현
