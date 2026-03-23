---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/WSL/wsl usb/","tags":["type/study","context/studies","theme/devenv","status/completed"]}
---


# WSL 2 + usbipd-win 으로 USB 장치 연결하기

## 📦 준비물 (필수 조건)

- Windows 11 (빌드 22000 이상)
    
    - Windows 10은 Microsoft Store WSL 버전으로 가능
        
- x64 프로세서
    
- WSL 2 설치 및 최신화
    
- 설치된 리눅스 배포판 (Ubuntu 등)
    

> **Windows 버전 확인:**  
> `윈도우 키 + R → winver`
> 
> **WSL 버전/커널 업데이트:**
> 
> ```bash
> wsl --update
> ```

---

## 🔥 설치하기 - usbipd-win

### 1. usbipd-win 설치

- [usbipd-win GitHub 릴리즈](https://github.com/dorssel/usbipd-win/releases) 접속
    
- `.msi` 설치 파일 다운로드 후 실행
    
- 또는 Winget으로 설치
    
    ```bash
    winget install --interactive --exact dorssel.usbipd-win
    ```
    

### 2. 설치 완료 시 구성되는 것

- Windows 서비스: `usbipd`
    
- CLI 명령어: `usbipd`
    
- 기본 방화벽 규칙 추가
    

---

## 🚀 USB 장치 WSL에 연결하기

### 1. 연결 가능한 USB 디바이스 목록 보기

```bash
usbipd list
```

- 원하는 디바이스의 **BUSID** 메모
    

### 2. 디바이스 공유하기

```bash
usbipd bind --busid <BUSID>
```

- **폰에서는 "USB 디버깅 허용" 팝업**이 뜨면 **확인**해야 함
    

### 3. WSL에 디바이스 붙이기

```bash
usbipd attach --wsl --busid <BUSID>
```

- 이 시점부터 **Windows에서는 사용 불가**
    
- **WSL 리눅스 안에서만 인식** 가능
    

### 4. 연결 확인

WSL에서:

```bash
lsusb
```

- 연결된 디바이스가 리스트에 보이면 성공!
    

---

## 🛑 연결 끊기

WSL에서 장치 사용을 끝냈으면:

```bash
usbipd detach --busid <BUSID>
```

또는 그냥 USB 케이블 뽑아도 됨.

## 에러사항
- 허브에 usb포트 연결시 윈도우에서는 adb가 인식하나 wsl에서 인식하지 못하거나 아래와 같은 메시지가 발생.
```
WSL usbip: error: Attach Request for <포트번호> failed - Device busy (exported)
usbipd: warning: The device appears to be used by Windows; stop the software using the device, or bind the device using the '--force' option.
usbipd: error: Failed to attach device with busid '<포트번호>'.
```


## 💡 참고

- 디바이스에 따라 추가 `udev` 설정이 필요할 수 있음 (ex: 권한 문제 해결)

- WSL이 종료되거나 재부팅하면 USB 디바이스 연결이 해제됨 (다시 attach 필요)

- 자세한 구조 이해는 [공식 Microsoft Docs](https://learn.microsoft.com/ko-kr/windows/wsl/connect-usb) 참고

---

# 📚 요약

|상황|명령어|
|:--|:--|
|USB 목록 보기|`usbipd list`|
|디바이스 공유|`usbipd bind --busid <BUSID>`|
|WSL에 연결|`usbipd attach --wsl --busid <BUSID>`|
|연결 해제|`usbipd detach --busid <BUSID>`|
