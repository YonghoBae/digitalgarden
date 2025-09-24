---
{"dg-publish":true,"permalink":"/project/setting/Docker/Docker Start(window)/"}
---


![Pasted image 20250615171841.png](/img/user/Image/Pasted%20image%2020250615171841.png)

![Pasted image 20250615171851.png](/img/user/Image/Pasted%20image%2020250615171851.png)
![Pasted image 20250615171907.png](/img/user/Image/Pasted%20image%2020250615171907.png)
# 🐳 Docker Desktop & WSL2 에러 해결 삽질기

## 시작하며

Windows 환경에서 처음으로 Docker를 사용해보기로 마음먹었다. 개발 환경을 컨테이너로 관리하는 현대적인 방식에 대한 기대가 컸다. 하지만 'Docker Desktop'을 설치하고 실행하자마자, 기대는 온데간데없고 온갖 종류의 에러 메시지가 나를 반겨주었다. 설치 과정부터 순탄치 않았던 그 험난한 여정과 해결 과정을, 미래의 나와 다른 입문자들을 위해 기록으로 남긴다.

---

## ❗ 마주했던 오류와 해결 과정

### 1. `waiting for "Ubuntu" distro to be ready: file not found`

_(스크린샷의 첫 번째 오류 메시지)_

Docker가 WSL의 Ubuntu 배포판을 인식하지 못하는, 가장 처음 마주한 오류였다. WSL 자체는 `wsl -d Ubuntu` 명령으로 잘 들어가지는데, 유독 Docker만 파일을 못 찾는다고 하니 답답한 노릇이었다.

- **추정 원인:** Ubuntu 배포판의 내부 파일 시스템 마운트(mount) 실패 또는 시스템 패키지 손상.
    
- **해결 과정:** 혹시나 하는 마음에 WSL Ubuntu 터미널에 들어가 `sudo apt update && sudo apt upgrade`를 실행했다. 한참 동안 패키지를 업데이트하더니, 거짓말처럼 문제가 해결됐다. WSL 시스템 파일이 일부 손상되었거나 업데이트가 필요했던 것으로 보인다.
    

### 2. `waiting for the Docker API: context deadline exceeded`

_(스크린샷의 두 번째 오류 메시지)_

첫 번째 문제를 해결하니 나타난 다음 오류. Docker Engine 자체가 제대로 부팅되지 못해 API가 응답할 시간을 초과했다는 의미였다.

- **추정 원인:** WSL과의 연동 과정에서 Docker Engine 프로세스가 제대로 시작되지 못하고 멈춰버린 상태.
    
- **해결 과정:** 이건 비교적 고전적인 해결법이 통했다. PowerShell이나 CMD에서 `wsl --shutdown` 명령으로 WSL을 완전히 내렸다가 Docker Desktop을 재시작하니 정상적으로 엔진이 구동되었다.
    

### 3. `com.docker.build: exit status 1`

_(스크린샷의 세 번째 오류 메시지)_

가장 골치 아팠던 오류다. Docker가 시작되면서 내부 모듈들이 서로 꼬이는, 일종의 경쟁 상태(race condition) 문제로 보였다. Docker Desktop 최신 버전의 WSL 연동 과정에서 발생하는 고질적인 이슈 중 하나인 듯했다.

- **추정 원인:** Docker Desktop 내부 모듈 간의 충돌.
    
- **해결 과정:** 작업 관리자에서 관련 프로세스를 강제 종료하는 방법도 통하지 않았다. 결국 다른 모든 방법을 포기하고 **Windows를 전체 재부팅**하니 허무하게 해결되었다. 역시 '껐다 켜기'는 진리다.
    

### 4. `The command 'docker' could not be found in this WSL 2 distro.`

이건 스크린샷에는 없지만, 위 문제들을 해결하는 과정에서 WSL Ubuntu 터미널에 `docker`를 입력했을 때 마주쳤던 오류다.

- **추정 원인:** Docker Desktop이 WSL 배포판 안으로 Docker CLI 명령어 경로를 제대로 심어주지 못했을 때 발생한다.
    
- **해결 과정:** **Docker Desktop 설정(Settings) > Resources > WSL Integration** 메뉴로 들어가, 'Ubuntu' 배포판 연동을 체크 해제했다가 다시 체크하는 것만으로 간단히 해결되었다. Docker가 관련 설정을 다시 주입해 주는 원리다.
    

---

## 🔄 그래서, 추천하는 해결 루틴

앞으로 비슷한 문제가 발생하면, 아래 순서대로 시도하는 것이 가장 효율적일 것이다.

> 1. **WSL 완전 종료**: PowerShell에서 `wsl --shutdown` 실행.
>     
> 2. **Docker Desktop 재시작**: 대부분의 API, Engine 문제는 여기서 해결된다.
>     
> 3. **WSL 배포판 업데이트**: 1, 2번으로 안 되면 WSL Ubuntu에 진입해 `sudo apt update && sudo apt upgrade` 실행.
>     
> 4. **Windows 전체 재부팅**: 위의 모든 방법으로 해결되지 않을 때 시도하는 최후의 수단. 하지만 가장 확실하다.
>     

## ✅ 결론

결국 대부분의 문제는 WSL 자체를 재시작하거나, OS를 재부팅하면 해결되었다. Docker Desktop과 WSL2는 아주 깊게 엮여있어, 한쪽의 문제가 다른 쪽의 오류로 나타나는 경우가 많다는 것을 체감했다. 이 기록이 나중의 나에게 삽질의 시간을 줄여주는 좋은 치트키가 될 것이다.