---
{"dg-publish":true,"permalink":"/project/setting/DevEnv/React-Native+wsl/"}
---


# React Native + WSL2 개발환경 구축

## WSL 설치
[[project/setting/WSL/wsl\|WSL 세팅]]


## Node.js 및 React Native 환경 구성

### Node.js 설치
[[project/setting/DevEnv/NodeJS\|NodeJS 세팅]]

### React Native CLI 설치

```bash
npm install -g react-native-cli
```

## Android SDK 설치 및 환경 변수 설정
- 처음에는 `sudo apt install android-sdk`로 설치를 시도했다.
- 시도한 결과 요구하는 버전은 35.0.0이었으나 29.0.3가 설치됐다.
- 그래서 cmdline-tools를 이용한 설치 방법을 시도 해봤다.

### Commandline-Tools 다운로드 및 설치

-  [공식 사이트](https://developer.android.com/studio#cmdline-tools)
![Pasted image 20250414225218.png](/img/user/Image/Pasted%20image%2020250414225218.png)
- WSL에서 설치하기 때문에 LInux를 선택하고

![Pasted image 20250414225245.png](/img/user/Image/Pasted%20image%2020250414225245.png)
- 해당 창에서 링크 주소 복사를 실행했다.

```bash
wget -O commandlinetools.zip "https://dl.google.com/android/repository/commandlinetools-linux-13114758_latest.zip?hl=ko"

```
- wget을 이용해서 다운받았다.

- 처음에는 `sudo apt install android-sdk`을 실행해서 설치되는 경로와 같은 경로에 설치할려고 했으나 설치는 루트 권한으로 잘 설치했는데 실행권한 문제 때문에 다른 문서에서 많이 하는 것처럼 사용자 디렉토리에 생성했다.
```bash
mkdir -p ~/android-sdk/cmdline-tools
unzip commandlinetools-linux-*.zip -d ~/android-sdk/cmdline-tools
mv ~/android-sdk/cmdline-tools/cmdline-tools ~/android-sdk/cmdline-tools/latest
```

### 환경 변수 설정 
```
sudo vi ~/.bashrc
```
- .bashrc에 아래 입력 후 저장
```
export ANDROID_HOME="/home/wolyong/android-sdk"
export PATH="${PATH} :${ANDROID_HOME}/cmdline-tools/tools/bin"
```

```bash
source ~/.bashrc
```

---

## Android SDK 필수 패키지 설치

- `/home/{사용자명}/android-sdk/cmdline-tools/latest/bin` 경로로 들어가면 sdkmanager가 있는데 이걸 이용해서 SDK 필수 패키지를 설치한다.
```bash
./sdkmanager --licenses
./sdkmanager "platform-tools" "platforms;android-35" "build-tools;35.0.0"
```

---

## ADB 설정 및 디바이스 연결 (USB 실기기 디버깅)
- 기본적으로 wsl은 컴퓨터의 USB 포트를 인식하지 못한다. 따라서 따로 설정이 필요하다.
- [[project/setting/WSL/wsl usb\|wsl USB 연결 설정]]

### WSL에서 장치 인식 확인

```bash
adb devices
```

### 포트 포워딩 설정 (Metro → 디바이스 연결)

```bash
adb reverse tcp:8081 tcp:8081
```


## React Native 프로젝트 실행

```bash
npx react-native init MyApp
cd MyApp
npm start    # Metro Bundler 실행
```

### 다른 터미널에서 앱 설치 및 실행

```bash
npx react-native run-android
```

---

## NDK 설치 이슈 및 권한 문제 해결 (선택적)

- `/usr/lib/android-sdk` 사용 시 권한 오류 발생:
    
    > `The SDK directory is not writable (/usr/lib/android-sdk)`
    

### 해결 방법

- SDK를 **홈 디렉토리(~/android-sdk)** 에 설치하고 사용자 권한으로 관리
    
- 필요한 경우 NDK 수동 설치:
    

```bash
sdkmanager "ndk;26.1.10909125"
```

---

## 🔄 기타 참고 명령어

|목적|명령어|
|---|---|
|SDK 설치 가능 목록 확인|`sdkmanager --list`|
|ADB 서버 재시작|`adb kill-server && adb start-server`|
|ADB 키 강제 초기화|`rm ~/.android/adbkey*`|
|USB 디바이스 공유 해제|`usbipd wsl detach --busid <번호>`|
|React Native Doctor 실행|`npx react-native doctor`|

---

## 최종적으로 개발 가능한 상태

- VS Code에서 소스 수정
- `npm start`로 Metro 서버 실행
- USB 연결된 안드로이드 디바이스에서 실시간 디버깅 및 테스트 가능
