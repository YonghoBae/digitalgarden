---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/CICD/Github Actions/GitHub Actions 배포 패턴/","tags":["type/study","context/studies","theme/devenv","status/completed"]}
---

# GitHub Actions 배포 패턴

> main 브랜치 push → Docker 이미지 빌드/Hub 푸시 → SSH로 서버 배포하는 자동화 패턴

## 워크플로우 기본 구조

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ "main" ]   # 트리거 조건

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      # ... 빌드 단계

  deploy:
    needs: build-and-push   # 이전 job 성공 시에만 실행
    runs-on: ubuntu-latest
    steps:
      # ... 배포 단계
```

## Docker Hub 빌드 & 푸시

```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v2

- name: Login to Docker Hub
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}

- name: Build and push Docker image
  uses: docker/build-push-action@v4
  with:
    context: .
    push: true
    tags: ${{ secrets.DOCKERHUB_USERNAME }}/myapp:latest
    cache-from: type=registry,ref=${{ secrets.DOCKERHUB_USERNAME }}/myapp:buildcache
    cache-to: type=registry,ref=${{ secrets.DOCKERHUB_USERNAME }}/myapp:buildcache,mode=max
```

- **Multi-stage Build**: Dockerfile에서 빌드 환경과 실행 환경 분리 → 이미지 용량 축소
- **Layer Caching**: 변경 없는 레이어는 Docker Hub 캐시 재사용 → 빌드 시간 ~50% 단축

## SSH 서버 배포

```yaml
- name: Deploy to Server
  uses: appleboy/ssh-action@master
  with:
    host: ${{ secrets.SERVER_HOST }}
    username: ${{ secrets.SERVER_USERNAME }}
    key: ${{ secrets.SSH_PRIVATE_KEY }}
    script: |
      echo "DATABASE_URL=${{ secrets.DATABASE_URL }}" > .env
      echo "JWT_SECRET=${{ secrets.JWT_SECRET }}" >> .env
      docker compose pull
      docker compose up -d
      docker image prune -f
```

- Secrets로 `.env` 파일을 동적 생성 → 코드에 민감 정보 하드코딩 방지
- `docker compose pull` + `up -d`: 변경된 컨테이너만 재시작

## 주요 트러블슈팅 팁

| 문제 | 원인 | 해결 |
|------|------|------|
| `ssh: no key found` | Secret에 공개키를 넣음 | `id_rsa`(개인키) 전체 내용 복사 (BEGIN/END 라인 포함) |
| `sudo: a password is required` | docker 명령에 sudo 필요 | `sudo usermod -aG docker $USER`로 docker 그룹 추가 |
| `Authentication failed` (git push) | HTTPS 방식은 deprecated | 원격 주소를 SSH 방식(`git@github.com:...`)으로 변경 |

## 관련 개념

- [[02 - Knowledge/DevEnv/CICD/CI-CD 개념\|CI-CD 개념]] — CI/CD 파이프라인 개념
- [[../Docker/Docker Start(window)\|Docker 트러블슈팅]]
