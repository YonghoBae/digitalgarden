---
{"dg-publish":true,"permalink":"/Software Engineering/Software Design/Software Design/"}
---


# Software Design
## Software Design이란
- 요구사항을 소프트웨어 표현으로 변환하는 과정

## Software Design에서 중요한 것
#### Design(설계)와 Quality(품질)는 서로 연결되어 있음 
- Design은 품질을 육성하는 곳(해석이 확실한가?)
- Design은 품질을 평가하는 SW 표현을 우리에게 제공



## Good Design이란
- 계층 구조
- 모듈
- 데이터와 절차의 명확하고 구분되어있는 표현 유지
- 독립전인 기능을 나타내는 모듈
- 복잡함을 줄인 인터페이스(모듈과 외부 환경 사이에서)
- 변경하기 쉽게(왜 하는가? 좋은 설계를 하는 이유? 모듈성이 높게하는 이유?)


## Modularity and Software Cost
![Pasted image 20241118114740.png](/img/user/Image/Pasted%20image%2020241118114740.png)

## Effective Modular Design(효과적인 모듈화)(효과적인 묘듈 설계)
- 기능의 독립성
- 추상화와 정보은닉의 개념 기반
#### 각 모듈이 가져야하는 것
- 요구사항에 대한 **하나의 특정(독립적) 기능**
- 바깥에서 볼 떄 **하나의 간단한 인터페이스**
![Pasted image 20241118174314.png](/img/user/Image/Pasted%20image%2020241118174314.png)
#### 측정기준
- [[Software Engineering/Software Design/Coupling\|Coupling]](결합력)
- [[Software Engineering/Software Design/Cohesion\|Cohesion]](응집력)
