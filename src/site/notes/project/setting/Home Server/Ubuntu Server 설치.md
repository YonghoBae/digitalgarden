---
{"dg-publish":true,"permalink":"/project/setting/Home Server/Ubuntu Server 설치/"}
---


# 낡은 노트북으로 구축한 개인 홈서버 (Ubuntu Server & Docker)

## 1. 프로젝트 동기

사용하던 `msi ps63 modern 8rc` 노트북의 힌지가 파손되어 휴대용으로 사용할 수 없게 되면서, 이를 활용한 홈서버 구축을 계획했습니다. 초기에는 Windows의 WSL 환경에서 개발 서버를 운영했지만, **마인크래프트 서버를 추가로 운영하면서 심각한 메모리 부족 문제**를 겪게 되었습니다.

이 문제를 해결하기 위해, 가볍고 안정적인 **Ubuntu Server 24.04 LTS**를 설치하여 리소스를 효율적으로 사용하는 홈서버 환경을 새롭게 구축했습니다.

## 2. 서버 환경

- **하드웨어:** MSI PS63 Modern 8RC Laptop (Intel i7-8565U, 16GB RAM, NVIDIA GeForce GTX 1050)
- **운영체제:** Ubuntu Server 24.04 LTS
- **핵심 기술:** Docker, Docker Compose, Nginx Proxy Manager

## 3. 서버 구축 과정

### 3.1. 부팅 USB 제작 및 OS 설치

- **Ubuntu Server 24.04 LTS** 버전의 ISO 파일을 다운로드하고, `Rufus`를 이용해 부팅 USB를 제작했습니다.
- 설치 과정에서는 LVM(논리 볼륨 관리자) 기반으로 디스크 전체를 사용하도록 설정하고, 원격 관리를 위해 **OpenSSH 서버를 기본적으로 설치**하는 옵션을 선택하여 진행했습니다.


### 3.2. 초기 설정 및 트러블슈팅

- **NVIDIA 드라이버 설치:** OS 설치 후, 내장 GPU(GTX 1050)의 정상적인 사용을 위해 아래 명령어로 공식 드라이버를 설치했습니다.
```
    sudo ubuntu-drivers autoinstall
```


 
- **Wi-Fi 드라이버 호환성 문제 해결:**
    
    - **문제 발생:** 서버 초기 설정 단계에서 내장 Wi-Fi 카드를 인식하지 못하는 문제가 발생했습니다.
    - **해결 과정:** 유선 랜을 임시로 연결한 뒤, 로그 분석을 통해 원인이 **Intel Wi-Fi 드라이버 펌웨어 부재**임을 파악했습니다. `linux-firmware` 패키지를 설치하여 문제를 해결했으며, 이 과정을 통해 하드웨어 레벨의 문제 해결 능력을 기를 수 있었습니다.
```
	sudo apt updatesudo
	apt install linux-firmware
```


## 4. 서비스 배포 및 운영 (Docker Compose 활용)

모든 서비스는 Docker 컨테이너 기반으로 운영하여, 서비스 간의 격리를 보장하고 배포 및 관리를 용이하게 했습니다.

### 4.1. 마인크래프트 서버 배포

안정적인 마인크래프트 서버 운영을 위해 가장 널리 사용되는 `itzg/minecraft-server` 이미지를 사용했습니다. `docker-compose.yml`을 통해 서버 설정을 코드로 관리하고, 서버 데이터는 Docker 볼륨을 이용해 호스트 머신에 안전하게 저장되도록 구성했습니다.

**`docker-compose.yml` (Minecraft)**

```
version: '3.8'

services:
  minecraft:
    image: itzg/minecraft-server
    container_name: mc-server
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
      MEMORY: "6G" # 서버에 할당할 최대 메모리
      VERSION: "1.20.1"
    volumes:
      - ./minecraft-data:/data # 월드 및 서버 데이터를 호스트와 연결
    restart: unless-stopped
```

### 4.2. 개인 블로그 백엔드 배포

직접 개발한 Node.js 기반의 블로그 백엔드 서버와 MySQL 데이터베이스를 `docker-compose`로 함께 묶어 배포했습니다.

- **`backend` 서비스:** `Dockerfile`을 이용해 직접 빌드한 이미지를 사용합니다.
    
- **`db` 서비스:** 공식 MySQL 이미지를 사용하며, 데이터는 Docker 볼륨을 통해 영속성을 확보합니다.
    
- **네트워킹:** 두 컨테이너는 Docker 내부 네트워크를 통해 안전하게 통신하며, 백엔드 서버만 외부 포트와 연결됩니다.
    

**`docker-compose.yml` (Blog Backend)**

```
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: blog-db
    volumes:
      - ./mysql-data:/var/lib/mysql # DB 데이터를 호스트와 연결
    environment:
      MYSQL_ROOT_PASSWORD: your_strong_password
      MYSQL_DATABASE: ohgnoy_db
    restart: unless-stopped

  backend:
    build: ./backend.ohgnoy # 백엔드 프로젝트 경로의 Dockerfile을 사용해 빌드
    container_name: blog-backend
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db # Docker 내부 네트워크의 서비스 이름으로 DB 호스트 지정
      DB_USER: root
      DB_PASSWORD: your_strong_password
      JWT_SECRET: your_jwt_secret
    depends_on:
      - db # db 컨테이너가 먼저 실행되도록 보장
    restart: unless-stopped
```

## 5. 네트워크 구성 (Nginx Reverse Proxy)

서버 내부에서 실행되는 여러 서비스(마인크래프트, 블로그 백엔드 등)를 외부에서 안전하고 편리하게 접근할 수 있도록 **Nginx Proxy Manager**를 도입했습니다.

- **역할:** 외부의 80(HTTP), 443(HTTPS) 포트로 들어오는 요청을 받아, 각 요청에 맞는 내부 서비스의 포트(e.g., `localhost:8080`)로 전달합니다.
    
- **장점:**
    - `blog.mydomain.com`과 같은 서브도메인을 각 서비스에 할당할 수 있습니다.
    - Let's Encrypt를 이용한 **무료 SSL 인증서 발급 및 자동 갱신**으로 모든 통신을 HTTPS로 암호화할 수 있습니다.