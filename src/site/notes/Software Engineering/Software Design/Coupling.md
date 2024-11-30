---
{"dg-publish":true,"permalink":"/Software Engineering/Software Design/Coupling/"}
---


# Coupling
- 두 모듈의 상호의존성 정도
- 서로 영향을 덜 받는게 좋기 때문에 결합력은 낮은게 좋음
#### Low Couping System(결합력이 낮아진다면)
- 파동 효과 예방
- 이해성 상승
	- why? 다른 모듈은 살펴볼 필요없이 해당 모듈만 확인하면 됨
#### Objective: Minimize coupling
- 가능한 독립적인 모듈들을 만들기
- 모듈이 잘 쪼개진 시스템

#### How to achieve low couppling(결합력을 낮추는 방법)
- 불필요한 관계 제거
- 필요한 관계의 수 줄이기


## Scale of Coupling
![Pasted image 20241118180815.png](/img/user/Image/Pasted%20image%2020241118180815.png)

## Message Coupling
- Message = **객체지향에서 메소드 호출**(**Public**)
- **가장 느슨한 타입**
- **객체**(상태 분산으로 달성?)
- **파라미터** 또는 **메시지 전달**같은 컴포넌트 커뮤니케이션
- 모듈은 서로 의존하지 않고 공개 인터페이스로 파라미터 없는 메시지를 교환

#### 내부x, 인터페이스 정의로만 이어져있음
![Pasted image 20241130015658.png](/img/user/Image/Pasted%20image%2020241130015658.png)

## Data Coupling
- **파라미터 = 기본 데이터 타입**(**심플한 데이터 타입**)
- 모듈들은 통신해야 함 => 데이터 커플링을 피할 수 없음 => 최소한 유지
```
int GetTotalArea()
{
	int totalArea = 0 ;
	for ( i = 0 ; i < count ; i ++ ) {
	totalArea += GetArea(shapes[i].width, shapes[i].height);//Data Coupling
}
return totalArea ;
}
```

![Pasted image 20241130020057.png](/img/user/Image/Pasted%20image%2020241130020057.png)

### Warnings on Data Coupling
#### 넓은 인터페이스를 피해라
- **파라미터를 많이 쓰는 것**
```
int ReadCustomerInfo(char* ID, char* name, char* address, char* phone, char* SSN) 
{
}
int StoreCustomerInfo(char* ID, char* name, char* address, char* phone, char* SSN) {
}
```
#### 해결: 구조체 사용
```
typedef struct _customerInfo {
char *name, *address, *phone, *SSN ;
} CUSTOMER_INFO ;

int ReadCustomerInfo(char* ID, CUSTOMER_INFO* info) {
}

int StoreCustomerInfo(char* ID, CUSTOMER_INFO info) {
}
```
#### TRAMP 데이터를 피해라
###### TRAMP 데이터
- 모듈이 원하지 않는 데이터
- 시스템 주변을 이동하는 정보 = 서플러 라운드
- **전달 목적에 의미가 없음 => 유지보수 관점에서 이해성이 떨어짐**
- 많은 모듈을 통과할수록 실수로 변경되거나 결함이 확산될 가능성 높음

#### 해결: 모듈 구조 재구조화


## Stamp Coupling
- **composite data**(**구조체**)로 패스
- 데이터 구조에 모호성이 없어야함
- 너무 넓은 인터페이스는 피해야함
- 약간의 간접성 도입?
```
typedef struct _customerInfo {
char *name, *address, *phone, *SSN ;
} CUSTOMER_INFO ;

int ReadCustomerInfo(char* ID, CUSTOMER_INFO* info) {
}

int StoreCustomerInfo(char* ID, CUSTOMER_INFO info) {
}
```

### Warnings on Stamp Coupling
#### 문제1: 필요없는 정보 같이 전달 => 아마 TRAMP 데이터?
```
typedef struct _customerInfo {
char *name, *address, *phone, *SSN ;
} CUSTOMER_INFO ;

int ValidatePhoneNumber(CUSTOMER_INFO* info)//고객 정보 전달
{
	/* 고객의 휴대폰 정보만 사용 */
}
```
- 연관없는 모듈 간 **의존성** 생성
- **모호성 상승**
- **융통성 하락**
- **불필요한 데이터 관여**
#### 해결1: 실제 사용되는 값만 전달

#### 문제2: 번들링
- **연관없는 데이터들의 모음**
- **쓸모없이 모호성만 상승**
```
int CalcTototalPurchaseCost(int pricePerItem, int dicountType, int itemCount, int tax)// 개당 가격, 할인타입, 개수, 세금 
{
}
```

```
typedef struct _stuff {
int pricePerItem, dicountType, itemCount, tax ;
} STUFF ;

int CalcTototalPurchaseCost(STUFF stuff) {
}
```
#### 해결2-1: 연관없는 데이터를 모으지 마라
- **구조체 이름에 따라도 모호해짐**
- **데이터 구조를 의미 있게 정의해야함**
#### 해결2-2: 간단한 데이터와 구조체 타입의 인터페이스 사용