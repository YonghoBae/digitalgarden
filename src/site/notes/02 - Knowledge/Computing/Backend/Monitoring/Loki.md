---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Loki/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Loki

> 로그 저장/조회 시스템 — Prometheus와 동일한 레이블 모델로 로그를 인덱싱

## 개념

| 구성 요소 | 역할 |
|----------|------|
| **Loki** | 로그 저장 및 조회 (메트릭처럼 라벨 기반 인덱싱, 내용은 비인덱싱) |
| **Promtail** | 로그 수집 에이전트 (파일/도커 로그 tail → Loki push) |
| **Grafana** | Loki 데이터소스 연결 → LogQL로 조회 |

> Prometheus가 메트릭을 Pull하는 것과 달리, Promtail이 로그를 **Push**하는 구조

---

## docker-compose

```yaml
services:
  loki:
    image: grafana/loki:3.1.1
    container_name: loki
    command: ["-config.file=/etc/loki/config.yml"]
    volumes:
      - ./loki/config.yml:/etc/loki/config.yml:ro
      - ./loki/data:/loki
    ports: ["3100:3100"]
    networks: [monitoring_net]
    restart: unless-stopped

  promtail:
    image: grafana/promtail:3.1.1
    container_name: promtail
    command: ["-config.file=/etc/promtail/config.yml"]
    volumes:
      - ./promtail/config.yml:/etc/promtail/config.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/log:/var/log:ro
    networks: [monitoring_net]
    restart: unless-stopped
```

---

## Loki 설정 (최소)

```yaml
server:
  http_listen_port: 3100

common:
  path_prefix: /loki
  storage:
    filesystem:
      chunks_directory: /loki/chunks
      rules_directory: /loki/rules

schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

ruler:
  storage:
    type: local
    local:
      directory: /loki/rules
  rule_path: /loki/rules-temp
  alertmanager_url: http://alertmanager:9093
```

---

## Promtail 설정 (도커 로그 수집)

```yaml
server:
  http_listen_port: 9080

clients:
  - url: http://loki:3100/loki/api/v1/push

positions:
  filename: /tmp/positions.yaml

scrape_configs:
  - job_name: docker
    static_configs:
      - targets: [localhost]
        labels:
          job: docker
          __path__: /var/lib/docker/containers/*/*-json.log
    pipeline_stages:
      - docker: {}               # 도커 JSON 로그 파싱
      - labels:
          container: container_name
          image: image_name

  - job_name: syslog
    static_configs:
      - targets: [localhost]
        labels:
          job: syslog
          __path__: /var/log/*.log
```

---

## Grafana 데이터소스 프로비저닝

```yaml
# grafana/provisioning/datasources/loki.yml
apiVersion: 1
datasources:
  - name: Loki
    type: loki
    access: proxy
    url: http://loki:3100
    isDefault: false
```

---

## LogQL 기본 쿼리

```logql
# 특정 컨테이너 로그
{job="docker", container="leafy_api"}

# ERROR 포함 로그만
{job="docker"} |= "ERROR"

# HTTP 5xx 카운트 (로그 기반)
sum by (container) (
  rate({job="docker"} |= " 5" |~ "HTTP/1.1\" 5[0-9]{2}" [5m])
)
```

---

## 관련 개념

- [[02 - Knowledge/Computing/Backend/Monitoring/_Monitoring\|Monitoring]] - 전체 스택
- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - 메트릭 수집 (Loki와 상호보완)
- [[02 - Knowledge/Computing/Backend/Monitoring/Grafana\|Grafana]] - 시각화 및 LogQL 쿼리
