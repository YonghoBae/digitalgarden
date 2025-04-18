---
dg-publish: true
---

# 지능형 지속 위협
## APT 공격
- 지능형 지속 위협(Advanced Persistent Threat)
- 다양한 IT 기술, 방식을 이용
- 조직적으로 경제적인 목적을 위해 다양한 보안 위협들을 생산
- 지속적으로 특정 대상에게 가하는 공격 기법
#### 2015년 이후
- 지능적으로 심리적 헛점을 파고드는 행위에 초점이 맞추어져 가는 추세
	- ex)  이메일 이용해 악성코드 심기, 강제로 암호화 시켜 데이터 날리기
- 기존의 보이스 피싱이나 전화사기와 구별됨
- 첨단매체인 PC나 스마트폰 등에서 전자 네트워크와 이메일을 사용해 보안 취약점이나 심리적인 약점을 파고드는 형태 통칭

#### Advanced(지능형)
- 각약각색의 침략 기술과 특화된 악성코드 이용
- 여러 공격 요소 중 목표에 적합한 공격 기술을 선택

#### Persistent(지속)
- 오랜 시간에 걸쳐 목표를 공격 => 성공률 극대화
- 목적이 달성 될 때까지 다양한 형태의 공격이 점진적, 은밀히 이루어짐

#### Threat(위협)
- 공격자는 보통 풍부한 자원 등을 가진 개인 / 조직들을 목표로 삼음
- 공격에 성공 할 경우 매우 큰 규모의 피해를 초래
- 컴퓨터 기술의 발전으로 자동화된 공격 도구와 향상된 공격기법 => 위협의 수준 증가

#### 특징
- 표적 지향의 공격이므로 방식을 규정하지는 않음
	- 하지만 다음 7가지 공격 요소를 도출
![Pasted image 20241125003737.png](/img/user/Image/Pasted%20image%2020241125003737.png)


## APT 수행 단계
#### 1. 표적 시스템 인식(Targeted System Recongnition)
- 침입 전 표적 시스템에 대한 정보 수집 활동
	- 기술적인 방법: 포트 스캔 -> 침입가능한 포트
	- 비기술적인 방법: Social engineering -> Spear phishing, USB 감염 등에 사용

#### 2. 초기 침투(Initial Intrusion)
- 실질적인 APT 공격이 이루어지는 단계
- 표적 시스템에 초기 침투를 수행
- 수집한 정보를 토대로 [[CBNU/3-2/information security/기말범위/Spear Phishing\|Spear Phishing]] 공격을 시도
- 주요표적
	- 보안담당자, 네트워크담당자 같은 표적 시스템을 관리하는 담당자
- 공격자는 주로 악성코드에 감염된 웹사이트나 문서를 이메일에 첨부하여 표적에게 전송

#### 3. 백도어 생성(Backdoor Establishment)
- 최초 침입 이후 공격자는 향후에 더욱 쉽게 시스템에 접근하기 위하여 백도어를 생성
- 공격자는 자신이 생성한 C&C(Command and control) Server와 표적 시스템이 직접적으로 정보를 교환
- 정상적인 트래픽처럼 보이는 암호화된 데이터를 C&C 서버와 주고받아 Antivirus제품의 감시망을 벗어나 정보를 쉽게 획득

#### 4. 내부 인식(Internal Recognition)
- 신뢰된 컴퓨터를 통해서 네트워크 스캐닝을 시도
- 내부 네트워크를 통해서 정찰이 이루어지며 신뢰되는 관계를 통해 표적 시스템의 네트워크 정보를 흭득

#### 5. 전이(Metastasis)
- 내부 정찰을 통해서 얻은 정보를 이용하여 주요 정보가 있는 End-Point로 악성코드를 전이 동일 네트워크를 사용하는 모든 End-Point를 감염
- 공격자는 원하는 정보를 유출시킬 준비를 마침

#### 6. 임무 완료(Mission Complete)
- 공격자의 목적에 맞는 행동을 성공적으로 수행하는 단계
- 공격자의 목적은 대표적으로 시스템 파괴와 기밀 정보의 유출
- Antivirus에 탐지되지 않게 하기 위해서 정보는 암호화, 파일압축, 파일 분할 등의 기법을 사용

#### 7. 숨김(Hiding)
- APT 공격은 지속적 공격을 위해서 특별한 이벤트가 없는 동안 악성 소프트웨어를 숨김
- 몇몇은 Rootkit을 포함(루트권한)
- Rootkit을 이용하여 Malware의 행동을 숨김

## APT 공격 사례
#### IceFog
- 일본과 대한민국의 정부출연연구소, 방위산업체, 조선해양사, 통신사 및 고도의 기술을 보유한 회사 등을 타켓으로 활동하고 있는 APT 공격
- 스피어피싱 이메일이 이용, 이메일에 첨부된 악성코드를 열거나 악성 웹사이트를 방문
- 시스템을 감염시킨 뒤 공격자는 감염 시스템의 특성을 파악하고 확인하기 위해 폴더 목록, 어댑터 목록, IP구성, 네트워크 정보 등을 윈도우 주소록, WAB파일, HWP, XLS, DOC등 문서파일 그리고 사용자 계정 자격 정보 등을 탈취

#### Net Traveler
- 항공 우주 관련 기술 및 에너지 생산 관련 데이터나 통신 관련 데이터를 탈취
- 이메일 주소를 확보한 후 악성코드가 포함된 첨부 파일의 클릭을 유도하는 메시지를 발송
- 이메일에 첨부되는 마이크로소프트 오피스 형식(확장자 doc, xls 등) 및 PDF 파일에 첨부
- 시스템에 침입할 경우 시스템 내 모든 정보에 대해 모니터링 실시뿐만 아니라 데이터 추출, 사용자의 키 입력 기록 확인 및 각종 문서 파일의 탈취 등도 가능

#### Stuxnet
- 시스템을 감염시켜 원자로의 PLC(Programmable Logic Controller)에 악의적인 프로그램을 삽입
- 사용된 취약점은 Window shell LNK 취약점, Window server service 취약점, Window printer spooler 취약점, 공유 네트워크 서비스 취약점
- 장치인 USB를 공격에 이용하여 네트워크 망이 분리되어 있는 시스템에도 악성코드를 전파
- Stuxnet은 대상 시스템이 아닌 경우에는 특별한 활동을 하지 않았으며 자신의 존재를 지우기 위해서 Rootkit을 사용

#### Duqu
- Duqu는 정보 유출을 목적으로 하여 표적 시스템의 정보를 수집
- 감염된 Micro Office 문서를 첨부하는 Spear Phishing을 통해 초기 침투를 시도
- 성공적으로 시스템에 잠입하고 백도어를 설치함으로써 C&C(Control and Command) 서버와 통신
- 패스워드와 같은 주요 정보를 수집하며, 수집된 정보는 공격자가 네트워크상의 다른 시스템에 접근할 권한을 얻는 데 사용


## APT 공격 특징
- 특정 조직에 최적화된 공격 수행
- 충분한 시간과 비용을 투자
- 조직 및 구성원 개인에 대한 충분한 정보 수집(**사회공학적인 방법⭐** 이용)
- 탐지 회피 기법을 병행하여 공격 수행
	-  low and slow  전략
	- 알려지지 않은 악성코드(Zero Day Attack) 사용
	- 이상징후를 파악하지 못하도록 장기간에 걸쳐 은밀히 활동
- 다양한 방향으로 공격, 사용자 및 Endpoint에 집중

## APT 공격 대응 방안
- 공격자가 원하는 정보에 접근하기까지 소요 시간 지연
	- 네트워크 분리(망 분리)
	- 내부 시스템 인증 강화
- 공격자가 원하는 정보에 접근하기 이전 단계에서 탐지/제거
	- 알려지지 않은 악성코드(Zero Day Attack) 탐지/제거
	- 악성코드/원격접속/명령 및 제어 지점 접근 트래픽 탐지/차단