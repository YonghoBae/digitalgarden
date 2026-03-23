---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/Docker/Nginx 리버스 프록시 패턴/","tags":["type/study","context/studies","theme/devenv","status/completed"]}
---

# Nginx 리버스 프록시 + Docker Compose 패턴

> 서브도메인별로 요청을 라우팅하고, 내부 네트워크를 분리하는 다중 서비스 구성 패턴

## 아키텍처

```
사용자 → DNS → Nginx 컨테이너(80/443)
                  ├─ app1.domain.com → App1 컨테이너
                  └─ app2.domain.com → App2 컨테이너
                                          └─ DB 컨테이너 (외부 차단)
```

- **포트 번호 대신 서브도메인**으로 서비스 구분 → 관리 편의성 ↑
- Nginx 컨테이너를 별도로 분리하여 독립 관리

## Docker Compose 네트워크 분리

```yaml
services:
  nginx_proxy:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    networks:
      - web_network

  app1:
    build: ./app1
    networks:
      - web_network        # Nginx와 통신
      - internal_db_network  # DB와 통신

  app1_db:
    image: mysql:8.0
    networks:
      - internal_db_network  # 외부 접근 차단

networks:
  web_network:
    external: true
  internal_db_network:
    internal: true           # 외부 노출 없음
```

- `web_network`: Nginx ↔ 앱 컨테이너 통신용
- `internal_db_network` (`internal: true`): DB는 외부에서 접근 불가

## 진단 명령어

```bash
# 컨테이너 내부 설정 파일 확인 (볼륨 마운트 검증)
docker exec nginx_proxy cat /etc/nginx/nginx.conf

# 포트 점유 프로세스 확인
sudo lsof -i :80

# Nginx 컨테이너 재시작 (설정 변경 반영 안 될 때)
docker compose restart nginx_proxy
```

> `docker compose up` / `reload`로 반영 안 될 때는 `restart`로 프로세스 재시작 필요

## 관련 개념

- [[02 - Knowledge/DevEnv/Docker/Docker Start(window)\|Docker 트러블슈팅]]
- [[02 - Knowledge/DevEnv/CICD/Github Actions/GitHub Actions 배포 패턴\|GitHub Actions 배포 패턴]]
