---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Java/콘솔 MVC 설계 원칙/","tags":["type/study","context/studies","theme/java","status/completed"]}
---

# 콘솔 MVC 설계 원칙

> 우테코 프리코스 미션에서 체득한 콘솔 기반 Java 애플리케이션 설계 교훈

## 구조 — Domain 우선 설계

```
Domain → Service → Controller → View
```

Domain을 먼저 뽑고 구조를 잡는 것이 핵심.
Controller에 로직을 쌓으면 나중에 테스트 불가능한 구조가 됨.

```
InputView     — 입력만 담당 (파싱 포함, 생성 X)
OutputView    — 출력만 담당 (안내 문구 포함)
Controller    — 전체 흐름 조율만 (비즈니스 로직 X)
Service       — 비즈니스 로직
Domain        — 도메인 규칙 + 검증 (Car, Cars, Lotto 등)
```

---

## 책임 분리 원칙

| 레이어 | 해야 할 것 | 하면 안 되는 것 |
|--------|-----------|----------------|
| InputView | 입력 받기, 파싱 | 객체 생성, 검증 |
| OutputView | 출력 | 로직 |
| Controller | 흐름 조율 | 비즈니스 로직 |
| Service | 비즈니스 로직 | 도메인 규칙 검증 |
| Domain | 자기 자신의 유효성 검증 | I/O |

---

## 빠른 프로토타입의 함정

"일단 만들고 나중에 리팩토링" 전략은 실패 확률이 높음.

- Controller에 로직이 쌓이면 메서드가 모두 `private`이 되어 **테스트 불가**
- 리팩토링 시 기능이 어디서 깨지는지 파악 불가
- 초반에 **최소 책임 분리**만 해두면 나중 리팩토링 비용이 극적으로 줄어듦

---

## 테스트 전략

```
기능 하나 구현 → 단위 테스트 바로 작성 → 다음 기능
```

테스트 코드의 역할:
- 리팩토링 시 회귀 버그 즉시 감지
- 어떤 기능이 깨졌는지 빠른 파악
- 구조가 테스트 가능한지 자체가 설계 품질 지표

> [!note]
> 테스트를 못 쓰는 이유가 테스트 코드 부재가 아니라, **테스트 불가능한 구조**를 만들었기 때문인 경우가 많다.

---

## 구현 순서 체크리스트

- [ ] 기능 목록 README에 정리
- [ ] 클래스 다이어그램 (Mermaid) 먼저 작성
- [ ] Domain 클래스부터 구현 + 단위 테스트
- [ ] Service → Controller → View 순으로 조립
- [ ] 기능 하나 완성할 때마다 테스트 추가

---

## 관련 개념

- [[02 - Knowledge/Computing/Java/OOP/일급 컬렉션\|일급 컬렉션]] - Domain 설계 패턴
- [[02 - Knowledge/Computing/Java/OOP/커스텀 예외 설계\|커스텀 예외 설계]] - 검증 실패 처리
