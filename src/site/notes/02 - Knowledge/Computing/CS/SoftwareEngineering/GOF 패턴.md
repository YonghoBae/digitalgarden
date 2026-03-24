---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/SoftwareEngineering/GOF 패턴/","tags":["type/study","context/studies","theme/cs","status/completed"]}
---

# GOF 디자인 패턴 (Gang of Four)

> 생성 / 구조 / 행위 패턴 23가지

## 생성 패턴 (Creational)

객체 생성 방식을 추상화

| 패턴 | 목적 |
|------|------|
| **Factory Method** | 객체 생성을 서브클래스에 위임 |
| **Abstract Factory** | 관련된 객체군 생성 인터페이스 제공 |
| **Singleton** | 하나의 인스턴스만 생성 보장 |
| **Prototype** | 기존 인스턴스를 복제하여 생성 |
| **Builder** | 복잡한 객체를 단계별로 생성 |

---

## 구조 패턴 (Structural)

클래스/객체를 조합해 더 큰 구조 형성

| 패턴 | 목적 |
|------|------|
| **Adapter** | 호환되지 않는 인터페이스 변환 |
| **Composite** | 개별/복합 객체를 동일하게 처리 (트리 구조) |
| **Bridge** | 추상화와 구현을 분리하여 독립적 변경 가능 |
| **Decorator** | 객체에 기능을 동적으로 추가 |
| **Facade** | 복잡한 서브시스템을 간단한 인터페이스로 |
| **Flyweight** | 대량 객체를 공유하여 메모리 절약 |
| **Proxy** | 객체 접근 제어 / 대리 실행 |

---

## 행위 패턴 (Behavioral)

객체 간 상호작용과 책임 분배

| 패턴 | 목적 |
|------|------|
| **Template Method** | 알고리즘 구조는 상위 클래스, 세부 구현은 하위 클래스 |
| **Observer** | 상태 변화 시 등록된 객체에 자동 알림 |
| **Strategy** | 알고리즘을 클래스로 캡슐화하고 동적 교체 |
| **Chain of Responsibility** | 처리 요청을 순차적으로 전달 |
| **Command** | 요청을 객체로 캡슐화 (undo/redo 가능) |
| **Mediator** | 객체 간 중앙 집중식 통신 관리 |
| **Memento** | 객체 상태 저장/복원 |
| **State** | 객체 상태에 따라 행동 변경 |
| **Visitor** | 객체 구조 변경 없이 새로운 동작 추가 |
| **Iterator** | 컬렉션 내부 구조 노출 없이 순회 |
| **Interpreter** | 언어 문법 해석/실행 |

---

## 자주 쓰이는 패턴 요약

```
싱글톤: getInstance() 로 하나의 인스턴스만 반환
팩토리: 어떤 객체를 만들지 서브클래스가 결정
옵저버: subject.notify() → 등록된 observer.update() 호출
전략: context.setStrategy(new ConcreteStrategy()) 로 런타임 교체
데코레이터: new LoggingDecorator(new BasicComponent()) 처럼 감싸기
```

## 관련 개념
- [[02 - Knowledge/Computing/CS/SoftwareEngineering/소프트웨어 설계\|소프트웨어 설계]] - 결합도/응집도 개선에 활용
- [[02 - Knowledge/Computing/Java/OOP/객체지향프로그래밍\|OOP]] - 패턴 구현 기반
