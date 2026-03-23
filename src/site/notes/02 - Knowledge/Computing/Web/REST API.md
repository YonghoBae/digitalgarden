---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/REST API/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

# REST 아키텍처 스타일의 이해와 설계 원칙

---

## ## 1. REST의 정의 (Definition of REST)

**REST**는 **RE**presentational **S**tate **T**ransfer의 약자로, 분산 하이퍼미디어 시스템(예: 월드 와이드 웹)을 위한 **소프트웨어 아키텍처 스타일(Architectural Style)**이다. 이는 프로토콜이나 표준이 아니며, 웹의 장점을 최대한 활용할 수 있는 아키텍처 설계의 원칙이자 제약 조건의 집합이다.

REST는 Roy Fielding의 2000년 박사 학위 논문에서 처음 소개되었으며, 웹이 어떻게 성공적으로 확장성을 확보했는지를 설명하고 그 원칙을 다른 분산 시스템 설계에 적용하기 위해 제안되었다.

---

## ## 2. REST의 핵심 구성 요소

REST 아키텍처는 세 가지 핵심 요소로 구성된다.

- **자원 (Resource)**: API가 정보를 제공하는 모든 대상. 문서, 이미지, 서비스 등 이름으로 식별할 수 있는 모든 개념을 자원이라 칭한다. 각 자원은 **URI(Uniform Resource Identifier)**를 통해 고유하게 식별된다.
    
    - 예: `/users/123`, `/posts/software-engineering`
        
- **표현 (Representation)**: 자원의 특정 시점 상태. 클라이언트와 서버는 자원 자체를 주고받는 것이 아니라, 자원의 **'표현'**을 교환한다. 자원의 표현은 주로 **JSON**이나 **XML** 형식을 사용하며, 어떤 형식인지는 **Content-Type** 헤더를 통해 명시된다.
    
- **행위 (Verb)**: 자원에 대한 행위는 **HTTP 메서드**를 통해 명시적으로 표현된다. 행위는 자원의 표현을 전송함으로써 자원의 상태를 변경하거나 조회하는 것을 의미한다.
    
    - `GET`, `POST`, `PUT`, `PATCH`, `DELETE` 등
        

---

## ## 3. REST를 정의하는 6가지 아키텍처 제약 조건

API가 진정으로 "RESTful"하다고 말하기 위해서는 다음의 6가지 제약 조건을 모두 만족해야 한다.

### ### 1. 클라이언트-서버 구조 (Client-Server)

클라이언트와 서버의 역할이 명확히 분리되어야 한다. 클라이언트는 사용자 인터페이스에 집중하고, 서버는 자원 관리에 집중한다. 이 분리를 통해 각 컴포넌트는 독립적으로 발전하고 확장될 수 있다.

### ### 2. 무상태성 (Stateless)

서버는 클라이언트의 **세션 상태를 저장하지 않는다.** 각 요청은 작업을 처리하는 데 필요한 모든 정보를 담고 있어야 하며, 서버는 이전 요청에 대한 정보를 기억할 필요가 없다. 이는 서버의 구현을 단순화하고, 가시성(visibility)과 신뢰성(reliability), 확장성(scalability)을 향상시킨다.

### ### 3. 캐시 가능성 (Cacheable)

클라이언트는 서버의 응답을 캐시할 수 있어야 한다. 서버 응답은 스스로 캐시 가능한지 아닌지를 명시해야 한다(`Cache-Control` 헤더). 캐싱은 성능을 향상시키고, 서버와 클라이언트 간의 상호작용을 효율적으로 만든다.

### ### 4. 계층화 시스템 (Layered System)

클라이언트는 최종 서버에 직접 연결되었는지, 중간의 프록시나 로드 밸런서 같은 계층을 통과하는지 알 수 없다. 이러한 계층화는 보안 정책 적용, 로드 밸런싱, 공유 캐시 관리 등을 통해 시스템의 확장성과 유연성을 향상시킨다.

### ### 5. 일관된 인터페이스 (Uniform Interface)

시스템 전체 아키텍처를 단순화하고 상호작용의 가시성을 높이는 핵심 제약 조건이다. 네 가지 하위 원칙으로 구성된다.

- **자원의 식별 (Identification of resources)**: URI를 통해 자원을 명확하게 식별해야 한다.
    
- **표현을 통한 자원 조작 (Manipulation of resources through representations)**: 클라이언트는 자원의 표현과 메타데이터를 통해 자원의 상태를 변경할 수 있다.
    
- **자기 서술적 메시지 (Self-descriptive messages)**: 각 메시지는 그 메시지를 어떻게 처리해야 하는지에 대한 충분한 정보를 포함해야 한다(예: `Content-Type`, HTTP 메서드).
    
- **HATEOAS (Hypermedia as the Engine of Application State)**: 클라이언트는 동적으로 변하는 애플리케이션의 상태를 하이퍼미디어(링크)를 통해 전이해야 한다. 즉, 서버 응답에는 관련된 다음 행동을 할 수 있는 링크가 포함되어야 한다. 이를 통해 클라이언트는 API의 구조를 미리 알 필요 없이 동적으로 API를 탐색할 수 있다.
    

### ### 6. 주문형 코드 (Code-On-Demand) - 선택 사항

서버가 클라이언트에 실행 가능한 코드(예: JavaScript)를 전송하여 클라이언트의 기능을 일시적으로 확장할 수 있다는 원칙이다. 이는 선택적으로 적용된다.

---

## ## 4. Richardson 성숙도 모델

Leonard Richardson이 제안한 이 모델은 API가 얼마나 REST의 원칙을 잘 따르고 있는지를 평가하는 척도이다.

- **Level 0: The Swamp of POX (Plain Old XML)**
    
    - HTTP 프로토콜을 원격 프로시저 호출(RPC)을 위한 터널로만 사용한다. 단일 URI 엔드포인트에 모든 요청을 `POST`로 보낸다.
        
- **Level 1: Resources**
    
    - 요청을 여러 URI로 분리하여 개별 '자원'의 개념을 도입한다. 하지만 여전히 모든 요청에 `POST` 메서드를 주로 사용한다.
        
- **Level 2: HTTP Verbs**
    
    - 자원에 대한 행위를 명시하기 위해 `GET`, `POST`, `PUT`, `DELETE` 등 다양한 HTTP 메서드를 의미에 맞게 사용한다. 대부분의 상용 "REST API"가 이 수준에 해당한다.
        
- **Level 3: Hypermedia Controls (HATEOAS)**
    
    - 응답에 HATEOAS 원칙을 적용하여, 클라이언트가 다음 행동을 할 수 있는 링크를 포함한다. 이 수준에 도달해야 진정한 RESTful API라고 할 수 있다.
        

### ## 결론

REST는 단순히 '예쁜 URI'와 JSON을 사용하는 기술이 아니다. 이는 **확장성, 유연성, 독립적 진화**를 목표로 하는 아키텍처 철학이다. 특히 **무상태성(Stateless)**과 **일관된 인터페이스(Uniform Interface)**, 그중에서도 **HATEOAS**는 REST의 잠재력을 최대한 활용하기 위해 반드시 이해해야 할 핵심 개념이다.