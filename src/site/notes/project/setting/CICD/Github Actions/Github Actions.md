---
dg-publish: true
---
# `git push` 하나로 완성되는 나만의 배포 파이프라인 구축기

## 왜 CI/CD를 구축해야만 했나?

솔직히 처음에는 필요성을 크게 느끼지 못했다. 서버에 SSH로 접속해서 `git pull` 받고, `docker compose restart` 하는 과정이 몇 분 걸리지 않았으니까. 하지만 프로젝트가 늘어나고 코드 수정이 잦아지면서 이 수작업은 점점 귀찮고 비효율적으로 느껴졌다. 명령어 오타라도 나는 날엔 하루종일 그것만 보고있고 배포하는 동안에는 다른 작업을 할 수도 없었다.

매번 5분 넘게 걸리던 이 수작업을 없애고, **오직 코드에만 집중할 수 있는 환경**을 만들고 싶었다. `git push`라는 익숙한 명령어 하나로, 테스트부터 배포까지 알아서 다 해주는 그런 환경 말이다.

## 빌드와 실행 환경의 완전한 분리

CI/CD를 구축하기로 마음먹고 가장 중요하게 생각한 원칙이 하나 있다.

> "서버는 코드를 빌드하는 곳이 아니라, 완성된 결과물을 실행만 하는 곳이어야 한다."

과거에는 서버에서 직접 소스 코드를 `git pull` 받아 `npm install`이나 `mvn package` 같은 빌드 명령어를 실행했다. 하지만 이 방식은 서버의 상태나 설치된 라이브러리 버전에 따라 빌드가 실패하는, 소위 **"내 컴퓨터에선 됐는데..."** 문제의 원인이 되곤 했다.

그래서 이번에는 **GitHub Actions의 가상 머신에서 모든 빌드 과정을 끝내고, 완제품인 Docker 이미지를 만들어 Docker Hub에 올리는 방식**을 채택했다. 서버는 그저 Docker Hub에서 검증된 이미지를 내려받아 실행만 하면 된다. 마치 공장에서 완제품을 만들어 현장에서는 조립만 하는 것처럼, 어떤 환경에서든 동일하게 작동하는 **불변적인(Immutable) 배포 단위**를 확보하여 안정성을 극대화했다.

## GitHub Actions 워크플로우 상세

우선 **'leafy' 프로젝트**의 `.github/workflows/` 디렉토리에 `deploy.yml` 파일을 만들어 CI/CD 파이프라인을 적용했다. 이 경험을 바탕으로, 추후 'ohgnoy'를 포함한 다른 프로젝트에도 동일한 파이프라인을 확장 적용할 계획이다.

```yml
# .github/workflows/deploy.yml

name: CI/CD Pipeline

# 1. 트리거 조건: main 브랜치에 push가 발생하면 실행
on:
  push:
    branches: [ "main" ]

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout a repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      # Docker Hub 로그인 (Secrets 사용)
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
          
      # 2. 빌드 & 푸시: Multi-stage build와 Layer Cache를 활용
      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/leafy:latest
          cache-from: type=registry,ref=${{ secrets.DOCKERHUB_USERNAME }}/leafy:buildcache
          cache-to: type=registry,ref=${{ secrets.DOCKERHUB_USERNAME }}/leafy:buildcache,mode=max

  deploy:
    # 3. 배포: build-and-push 작업이 성공해야만 실행
    needs: build-and-push
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            # GitHub Secrets의 내용을 .env 파일로 생성
            echo "DATABASE_URL=${{ secrets.DATABASE_URL }}" > .env
            echo "JWT_SECRET=${{ secrets.JWT_SECRET }}" >> .env
            
            # 최신 이미지를 pull 받고, 변경된 컨테이너만 재시작
            docker compose pull
            docker compose up -d
            
            # 불필요한 이전 이미지 정리
            docker image prune -f
```

### 파이프라인의 주요 단계

1. **트리거**: `main` 브랜치에 코드가 `push`되면 파이프라인이 자동으로 시작된다.
2. **빌드 및 푸시 (Build & Push)**: 
    - **Multi-stage Build**: `Dockerfile` 내에서 빌드용 환경과 실행용 환경을 분리했다. 최종 이미지에는 불필요한 개발 도구 없이 오직 실행에 필요한 파일만 담아 용량을 줄이고 보안을 강화했다.
    - **Layer Caching**: 코드 변경이 없는 Docker 이미지 레이어는 캐시에서 가져와 재사용했다. 이를 통해 빌드 시간을 평균 50% 이상 단축시킬 수 있었다.

3. **배포 (Deploy)**:
    - 빌드가 성공하면, SSH로 개인 서버에 안전하게 접속한다.
    - GitHub Secrets에 저장해 둔 민감한 정보(DB 접속 주소 등)를 서버에 `.env` 파일로 동적으로 생성하여, 코드에 하드코딩하는 것을 방지했다.
    - `docker compose pull`로 방금 Docker Hub에 올라간 최신 이미지를 내려받고, `docker compose up -d`로 서비스 전체를 중단 없이(zero-downtime) 업데이트한다.

## 삽질의 기록: 주요 디버깅 과정

파이프라인 구축은 결코 순탄치 않았다. 수많은 에러 메시지들이 나를 반겨주었다. 그중 가장 기억에 남는 세 가지 삽질의 기록이다.

### 1. `ssh: no key found`

- **문제**: GitHub Actions가 서버에 SSH 접속을 못 하는 문제. 분명히 `SSH_PRIVATE_KEY` Secret에 키를 잘 넣었다고 생각했다.
- **원인과 해결**: 알고 보니, `id_rsa.pub`(공개키)가 아닌 `id_rsa`(개인키)의 **내용 전체**를 복사해야만 했다. 심지어 `-----BEGIN OPENSSH PRIVATE KEY-----` 부터 `-----END OPENSSH PRIVATE KEY-----` 라인과 마지막 줄바꿈까지 정확하게 포함해야 했다. 바보같은 실수였다.
    

### 2. `sudo: a password is required`

- **문제**: SSH 접속은 성공했는데, `docker compose` 명령어를 실행할 권한이 없다며 비밀번호를 요구하는 상황. 자동화 스크립트에서 비밀번호를 입력할 수는 없으니 막막했다.
- **원인과 해결**: 처음에는 `visudo` 파일을 수정해 `NOPASSWD` 옵션을 주는 방법을 생각했지만, 보안상 찜찜했다. 더 나은 방법을 찾아보니, **현재 사용자를 `docker` 그룹에 추가**(`sudo usermod -aG docker $USER`)하면 `sudo` 없이 `docker` 명령어를 실행할 수 있다는 것을 알게 되었다. 훨씬 깔끔하고 안전한 해결책이라고 생각한다.


### 3. Git 인증 `Authentication failed`

- **문제**: 이건 CI/CD와는 별개로, 서버 터미널에서 `git push`를 할 때 발생하던 문제다.
- **원인과 해결**: 원격 저장소 주소가 `https://github.com/...` 형태로 되어 있었기 때문. 비밀번호 인증 방식은 보안상 deprecated 되었다. 저장소 주소를 SSH 방식(`git@github.com:...`)으로 변경하고 서버에 SSH 키를 등록하여 해결했다.


## 결론 및 다음 단계

포트 충돌이라는 작은 문제에서 시작된 여정은, Nginx 리버스 프록시, Docker Compose, 그리고 마침내 GitHub Actions를 이용한 완전 자동화 배포 파이프라인 구축으로 이어졌다. 특히 CI/CD를 구축하며 겪었던 수많은 인증과 권한 오류들을 해결하며, 서버 환경과 자동화 스크립트의 작동 원리를 뼛속 깊이 이해하는 계기가 되었다.

여기서 멈추지 않고, 앞으로 아래와 같은 개선 작업을 통해 파이프라인을 더욱 고도화할 계획이다.
- **결과 알림**: 배포 성공/실패 시 **Discord 웹훅**을 연동하여 결과를 실시간으로 받아보는 알림 기능을 추가할 것이다.
- **환경 분리**: `develop` 브랜치는 개발(Staging) 서버에, `main` 브랜치는 실서버(Production)에 배포되도록 파이프라인을 확장하여 더 안정적인 운영 체계를 갖출 것이다.