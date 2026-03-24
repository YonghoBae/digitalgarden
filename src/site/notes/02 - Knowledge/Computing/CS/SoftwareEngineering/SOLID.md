---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/SoftwareEngineering/SOLID/","tags":["type/study","context/studies","theme/cs","status/completed"]}
---

# SOLID 원칙

> 좋은 객체 지향 설계를 위한 5가지 원칙

## SRP — 단일 책임 원칙 (Single Responsibility Principle)

- 한 클래스는 **하나의 책임**만 가져야 한다
- 기준: **변경**이 있을 때 파급 효과가 적어야 함

## OCP — 개방-폐쇄 원칙 (Open/Closed Principle)

- 소프트웨어 요소는 **확장에는 열려있고, 변경에는 닫혀** 있어야 한다
- 다형성을 활용하여 인터페이스의 새로운 구현체를 통해 기능 확장

## LSP — 리스코프 치환 원칙 (Liskov Substitution Principle)

- 프로그램의 객체는 하위 타입의 인스턴스로 **바꿔도 프로그램의 정확성이 깨지지 않아야** 한다
- 다형성에서 하위 클래스는 인터페이스 규약을 반드시 지켜야 함
- 단순히 컴파일 성공을 넘어, 의미론적 계약 준수

## ISP — 인터페이스 분리 원칙 (Interface Segregation Principle)

- **특정 클라이언트를 위한 인터페이스 여러 개**가 범용 인터페이스 하나보다 낫다
- 예: 자동차 인터페이스 → 운전 인터페이스 + 정비 인터페이스로 분리
  - 정비 인터페이스가 변해도 운전자 클라이언트에 영향 없음
- 인터페이스가 명확해지고 대체 가능성이 높아짐

## DIP — 의존관계 역전 원칙 (Dependency Inversion Principle)

- 프로그래머는 **추상화에 의존해야지, 구체화에 의존하면 안 된다**
- 구현 클래스에 의존하지 말고, **인터페이스(역할)에 의존**하라
- 의존성 주입(DI)은 이 원칙을 따르는 방법 중 하나

## 관련 개념

- [[02 - Knowledge/Computing/CS/SoftwareEngineering/UML\|UML]] — 인터페이스, 클래스 관계 표현
- [[02 - Knowledge/Computing/CS/SoftwareEngineering/소프트웨어 설계\|소프트웨어 설계]] — 응집도, 결합도
- [[02 - Knowledge/Computing/CS/SoftwareEngineering/GOF 패턴\|GOF 패턴]] — SOLID를 구현하는 패턴들
