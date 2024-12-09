---
{"dg-publish":true,"permalink":"/information security/기말범위/FireWall/"}
---


# 방화벽과 침입 방지 시스템
## 방화벽의 필요성
- 인터넷 연결이 필수 => 취약점 발생
- LAN 환경을 보호하는 효과적인 방법
- 제어 링크를 설정하는 내부 네트워크와 인터넷 사이에 삽입
	- 하나의 컴퓨터 시스템 또는 두 개 이상의 함께 작업하는 시스템에 설정 가능
- 주변 방어에 이용
	- 보안 및 감사에 이용하는 단일 초크포인트
		- 필수적으로 지나야하는 단일 지점
	- 외부 네트워크로부터 내부 네트워크를 보호

## 방화벽 특징
#### 설계 목표
- 외부에서 내부로의 모든 트래픽이 방화벽을 통과
- 로컬 보안 정책에 정의된 인증된 트래픽은 통과할 수 있어야함
- 방화벽 자체가 침입에 면역
#### 방화벽에서 액세스 제어, 보안 정책 적용할 기술
- IP주소와 프로토콜 값: 서비스 제어
- 응용프로토콜: 방향 제어
- 사용자식별: 사용자 인증 및 제어
- 네트워크활동: 동작 제어

## 방화벽의 기능과 한계
#### 기능
- 단일 초크 포인트
- 보안 이벤트 모니터링, 위치 제공
- 보안과 관련되지 않은 여러 인터넷 기능을 위한 편리한 플랫폼
- IPSec을 위한 플랫폼 제공 가능
#### 한계
- 방화벽을 우회하는 공격으로부터 보호 불가능
- 내부 위협으로부터 완전히 보호할 수 없음
- 적절하지 않게 보호된 무선 LAN은 외부조직으로부터 액세스할 수 있음
- 노트북, PDA, 휴대용 저장장치는 외부에서 감염되어 내부에서 이용될 수 있음

## 방화벽의 유형
![Pasted image 20241207221844.png](/img/user/Image/Pasted%20image%2020241207221844.png)

#### (a) General Model (일반 모델)
- 내부 네트워크(보호된 네트워크)와 외부 네트워크(신뢰할 수 없는 네트워크, 예: 인터넷) 사이에 위치하여 데이터의 흐름을 제어
#### (b) Packet Filtering Firewall (패킷 필터링 방화벽)
- 네트워크 계층에서 동작하며, IP 주소, 포트 번호, 프로토콜 등 패킷의 헤더 정보를 기반으로 트래픽을 허용하거나 차단.
#### (c) Stateful Inspection Firewall (상태 기반 방화벽)
- 트래픽의 상태를 추적하며, TCP/UDP 연결 상태 정보를 기반으로 패킷을 필터링.
#### (d) Application Proxy Firewall (애플리케이션 프록시 방화벽)
- 애플리케이션 계층에서 동작하며, 클라이언트와 서버 간의 중재 역할을 수행.
#### (e) Circuit-Level Proxy Firewall (회로 수준 프록시 방화벽)
- 세션 계층에서 동작하며, TCP 또는 UDP 연결을 승인하거나 차단.

## 패킷 필터링 방화벽

#### 방화벽 필터링 규칙
- 방화벽은 **IP 패킷**에 포함된 정보를 기반으로 **규칙을 적용**
	- IP or TCP 헤더와 매치한 규칙의 리스트
	- 규칙의 매치를 기반으로 전송  or 폐기
#### 필터링 규칙의 주요 요소
- 송신자 IP 주소
- 수신자 IP 주소
- 송수신 전송 계층 주소: TCP/UDP 포트 번호.
- IP 프로토콜 필드: 사용하는 네트워크 프로토콜(예: TCP, UDP, ICMP).
- 인터페이스: 패킷이 통과하는 네트워크 인터페이스(예: LAN, WAN).
#### 장점
- 간단함
- 일반적으로 사용자에게 투명하고 매우 빠름
#### 단점
- 응용프로그램의 특정 취약점 또는 기능을 이용한 공격을 방지할 수 없음
- 제한적인 로깅 기능
- 고급 사용자 인증을 지원하지 않음
- TCP/IP 프로토콜 버그를 이용한 공격에 취약
- 부적절한 설정이 위반으로 이어짐
#### 두 가지 기본 정책

| 정책 유형        | 설명             | 특징          | 사용 사례        |
| ------------ | -------------- | ----------- | ------------ |
| **기본 제거 정책** | 명확히 허용된 경우만 허용 | 보수적, 보안성 높음 | 정부, 군, 기업    |
| **기본 전달 정책** | 명확히 금지된 경우만 차단 | 유연함, 관리가 쉬움 | 대학교, 공공 네트워크 |

| Rule | Direction | Src Address | Dest Address | Protocol | Dest Port | Action |
|:----:| --------- | ----------- | ------------ | -------- | --------- | ------ |
|  1   | In        | External    | Internal     | TCP      | 25        | Permit |
|  2   | Out       | Internal    | External     | TCP      | >1023     | Permit |
|  3   | Out       | Internal    | External     | TCP      | 25        | Permit |
|  4   | In        | External    | Internal     | TCP      | >1023     | Permit |
|  5   | Either    | Any         | Any          | Any      | Any       | Deny   |

## 패킷필터링 방화벽에 대한 공격 형태
#### IP 주소 Spoofing
- 시스템 내부 사용자의 호스트 주소를 출발지address로 spoofing => 내부망 침투
#### 출발지 routing attack 
- IP, 포트 정보만 분석하고 routing 정보를 분석하지 않는 방화벽에 침투
	- IP, 포트 정보 = 목적지
	- routing 정보 = 경로
- 방화벽을 우회하는 detour route로 공격
	- detour route란 차단된 경로를 우회하여 목표 시스템에 도달하는 대체 경로
#### Tiny fragment attack 
- 방화벽이 TCP/IP 헤더 정보를 확인할 수 없도록 
- 헤더의 사이즈보다 작은 크기로 패킷을 fragmentation

## 상태 기반 검사 방화벽
#### 아웃바운드 TCP 연결
#### 디렉토리 생성
- TCP 트래픽에 대한 엄격한 규칙
- 각각 현재 설정된 연결의 항목
- 패킷 필터
	- 디렉토리 항목 중 프로파일과 동일한 패킷에 대해
	- 높은 번호(1024번 이상)의 포트로 들어오는 트래픽을 허용
		- 높은 번호의 포트로 서버가 응답하기 때문에
		- 서버 수신은 낮은 번호로 대기
#### 패킷 정보 리뷰
#### TCP 연결에 대한 정보 기록
- 시퀀스 넘버에 따른 공격 방지 위해  TCP 시퀀스 넘버 추천
	- 시퀀스 넘버는 데이터 순서를 결정
- 프로토콜 데이터 검사
	- ex) FTP, IM, SIPS 명령

|**Source Address**|**Source Port**|**Destination Address**|**Destination Port**|**Connection State**|
|---|---|---|---|---|
|192.168.1.100|1030|210.9.88.29|80|Established|
|192.168.1.102|1031|216.32.42.123|80|Established|
|192.168.1.101|1033|173.66.32.122|25|Established|
|192.168.1.106|1035|177.231.32.12|79|Established|
|223.43.21.231|1990|192.168.1.6|80|Established|
|219.22.123.32|2112|192.168.1.6|80|Established|
|210.99.212.18|3321|192.168.1.6|80|Established|
|24.102.32.23|1025|192.168.1.6|80|Established|
|223.21.22.12|1046|192.168.1.6|80|Established|

## 응용계층 게이트웨이(=응용  프록시)
#### 응용계층 트래픽의 중계
- 프로토콜 데이터 중계
- TCP/IP 응용을 이용한 사용자 접촉 게이트웨어
- 서버와 클라이언트 간 직접적인 end-to-end TCP 커넥션 허용x
- 사용자인증
- 원격 호스트와 중계 TCP 세그먼트의 게이트웨이 접촉 어플리케이션
#### 각 응용에 대한 프록시 코드가 있어야함
- 프록시코드는 중계를 위한 코드
	- ex) 프로토콜 처리 관련 코드
- 어플리케이션의 기능의 지원이 제한 가능
	- ex) 지원하지 않는 형식일 경우
- 패킷필터보다 안전한 경향
###### 단점
- 중계에 대한 연결 추가 처리 오버헤드 발생

## 서킷 레빌 게이트웨이
#### 서킷 레벨 프록시
- 두 TCP 연결 설정
- 내부 호스트와 외부 호스트 하나 사이에 설정
- 직접적인 end-to-end TCP 커넥션 허용X
- 내용 검사 없이 다른 하나의 연결로부터 TCP 세그먼트 중계
- 보안 기능은 연결 허용의 결정으로 구성
- 내용분석X(응용레벨게이트웨이와 다른점)
#### 내부사용자가 신뢰할 때 새용
- 응용 계층 게이트웨이 인바운드와 서킷 레벨 게이트웨이 아웃바운드에 이용
- 낮은 오버헤드

## 베스천 호스트
- 시스템 식별은 네트워크 보안의 중요한 장점
- 응용 계층 or 서킷 레벨 게이트웨이의 플랫폼 역할