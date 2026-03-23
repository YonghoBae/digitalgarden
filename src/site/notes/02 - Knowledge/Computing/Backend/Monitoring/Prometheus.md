---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Prometheus/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Prometheus

> 메트릭 수집 설정 파일 (`prometheus.yml`) 기반 Pull 방식 모니터링

## 구성 요소

| 섹션 | 설명 |
|------|------|
| `global` | 기본 수집 주기 / 규칙 평가 주기 |
| `scrape_configs` | 수집 대상 목록 정의 (핵심) |
| `alerting` | Alertmanager 연결 정보 |
| `rule_files` | Alert Rule 파일 경로 등록 |

---

## global

```yaml
global:
  scrape_interval: 15s          # 기본 수집 주기
  evaluation_interval: 15s      # 룰 평가 주기
  scrape_timeout: 10s           # (옵션) 수집 타임아웃
```

---

## scrape_configs

수집 대상 (Exporter, App 등)

```yaml
scrape_configs:
  - job_name: 'home-server'
    static_configs:
      - targets: ['node-exporter:9100', 'cadvisor:8080']
```

| 키 | 설명 |
|----|------|
| `job_name` | 수집 대상 그룹 이름 |
| `static_configs.targets` | 대상 주소 리스트 (서비스명:포트) |
| `scrape_interval` | 이 job만 별도 수집주기 적용 가능 |

---

## rule_files

Alert Rule 연결 → 등록 안 하면 rules.yml 작성해도 경보 작동 안 함

```yaml
rule_files:
  - '/etc/prometheus/rules.yml'
```

---

## alerting

Alertmanager 연결

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']
```

---

## 최소 실행 템플릿

Node Exporter + cAdvisor + Blackbox + MySQL + Redis 수집

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - /etc/prometheus/rules.yml

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

scrape_configs:

  # HOST METRICS (CPU/RAM/DISK/온도)
  - job_name: 'host'
    static_configs:
      - targets: ['node-exporter:9100']

  # DOCKER CONTAINERS
  - job_name: 'containers'
    static_configs:
      - targets: ['cadvisor:8080']

  # API HEALTHCHECK (HTTP Probe)
  - job_name: 'api-probe'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - http://api:3000/health
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: instance
        replacement: api
      - target_label: __address__
        replacement: blackbox:9115

  # MYSQL (선택)
  - job_name: 'mysql'
    static_configs:
      - targets: ['mysqld-exporter:9104']

  # REDIS (선택)
  - job_name: 'redis'
    static_configs:
      - targets: ['redis-exporter:9121']
```

---

## 응용 문법

| 목적 | 문법 |
|------|------|
| 특정 대상 제외 | `drop_labels`, `relabel_configs` |
| 서비스 디스커버리 | `docker_sd_configs`, `kubernetes_sd_configs` |
| 인증 헤더 필요 API | `basic_auth`, `bearer_token_file` |
| 장기 저장소 | `remote_write:` |

## 관련 개념
- [[02 - Knowledge/Computing/Backend/Monitoring/_Monitoring\|Monitoring]] - 전체 스택 흐름
- [[02 - Knowledge/Computing/Backend/Monitoring/PromQL\|PromQL]] - 수집된 데이터 조회
- [[02 - Knowledge/Computing/Backend/Monitoring/Alertmanager\|Alertmanager]] - 경보 라우팅
- [[02 - Knowledge/Computing/Backend/Monitoring/Exporter\|Exporter]] - 수집 대상별 설정
