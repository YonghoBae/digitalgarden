---
{"dg-publish":true,"permalink":"/project/setting/DevEnv/IntelliJ+wsl/"}
---



## WSL에서 JDK + IntelliJ 개발환경 설정

---

### 1. JDK 설치 (WSL Ubuntu)

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

> 필요한 경우 JDK 경로 수동 확인:

```bash
sudo update-alternatives --config java
```

---

### 2. IntelliJ에서 WSL 프로젝트 생성

1. IntelliJ 실행 → **New Project**
    
2. 프로젝트 유형 선택 (Java, Maven 등)
    
3. SDK 선택:  
    → **Add JDK → WSL** 선택  
    → `/usr/lib/jvm/...` 자동 인식 or 수동 지정
    
4. 프로젝트 저장 위치 설정:  
    → 예시: `\\wsl$\Ubuntu\home\user\myproject`
    

---

### 3. IntelliJ에서 코드 실행 (WSL 환경)

- **Run** 버튼 클릭 → 자동 WSL 실행
    
- **로그 확인**: Run 창에서 콘솔 확인
    
- **직접 실행 (WSL 터미널)**:
    

```bash
cd ~/myproject
java -jar myapp.jar
```

---

### 4. IntelliJ에 WSL 터미널 설정

- File → Settings → Tools → Terminal
    
- Shell path 설정:
    

```bash
wsl.exe
```

- 특정 배포판 지정 시:
    

```bash
wsl.exe -d Ubuntu
```

---

### 5. WSL에서 Git 설치 + IntelliJ 연동

```bash
sudo apt install git -y
```

- IntelliJ → Settings → Version Control → Git
    
- Git 실행 파일:
    

```
\\wsl$\Ubuntu\usr\bin\git
```

> GitHub 사용 시 SSH Key 연동 필요

---

### 6. 방화벽 설정 (WSL 네트워크/디버깅용)

```powershell
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound -InterfaceAlias "vEthernet (WSL)" -Action Allow
```

디버깅 포트 허용 (예: IntelliJ):

```powershell
Get-NetFirewallProfile -Name Public | Get-NetFirewallRule | where DisplayName -ILike "IntelliJ IDEA*" | Disable-NetFirewallRule
```

---


## 참고 문서

- [IntelliJ WSL 개발환경 공식 가이드](https://www.jetbrains.com/help/idea/how-to-use-wsl-development-environment-in-product.html)