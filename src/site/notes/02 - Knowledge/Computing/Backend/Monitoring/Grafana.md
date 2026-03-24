---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Grafana/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Grafana

> Prometheus 메트릭 시각화 + 대시보드 생성

## 핵심 개념

| 요소 | 설명 |
|------|------|
| **Data Source** | 데이터를 가져오는 곳 (여기서는 Prometheus) |
| **Panel** | 그래프/게이지/표 등 시각화 단위 |
| **Dashboard** | 여러 패널을 묶은 화면 |
| **Variable** | 대시보드 필터 (서버 선택 드롭다운) |

---

## docker-compose 템플릿

```yaml
services:
  grafana:
    image: grafana/grafana:11.1.4
    container_name: grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_PATHS_PROVISIONING=/etc/grafana/provisioning
    volumes:
      - ./grafana/data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    ports:
      - "3000:3000"
    restart: unless-stopped
    networks:
      - monitoring_net
```

---

## Prometheus 데이터소스 등록

`grafana/provisioning/datasources/prometheus.yml` (자동 프로비저닝)

```yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

수동 등록: Settings → Data Sources → Add → Prometheus

---

## 대시보드 구조 패턴

```
Dashboard
 ├─ Row: Host Metrics
 │   ├─ CPU % (graph)
 │   ├─ Memory % (gauge)
 │   ├─ Disk % (bar gauge)
 ├─ Row: Containers
 │   ├─ Container CPU % (table)
 │   ├─ Container Memory (table)
 ├─ Row: API / Services
 │   ├─ Probe Success (stat)
 │   ├─ Latency P95 (graph)
 └─ Row: DB / Redis (optional)
```

---

## Panel 쿼리 예시

| 패널 | PromQL |
|------|--------|
| CPU (graph) | `100 - (avg by (instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` |
| Memory (gauge) | `(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100` |
| Disk % (bar gauge) | `(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100` |
| Container CPU Top 5 | `topk(5, sum by (container)(rate(container_cpu_usage_seconds_total{container!=""}[5m])) * 100)` |
| API Health (stat) | `probe_success{instance="api"}` |
| API Latency P95 | `histogram_quantile(0.95, sum by (le)(rate(http_request_duration_seconds_bucket[5m])))` |

---

## 변수 (서버 선택 기능)

변수 설정: Dashboard Settings → Variables → New

| Name | Type | Query |
|------|------|-------|
| `instance` | Query | `label_values(node_cpu_seconds_total, instance)` |

쿼리에서 사용:

```promql
rate(node_cpu_seconds_total{mode="idle", instance="$instance"}[5m])
```

---

## 대시보드 Import (ID 입력)

Grafana → "Import Dashboard" → ID 입력 → Load

| 목적 | ID |
|------|----|
| Node Exporter Full | **1860** |
| cAdvisor / Containers | **14282**, **193** |
| Blackbox Exporter | **7587** |
| MySQL | **7362** |
| Redis | **763** |

---

## 자동 배포 (JSON 프로비저닝)

```
grafana/provisioning/dashboards/
└── dashboards.yml
└── dashboards/
    ├── host.json
    ├── containers.json
    └── services.json
```

`dashboards.yml`:

```yaml
apiVersion: 1
providers:
  - name: 'default'
    type: file
    folder: 'Imported'
    options:
      path: /etc/grafana/provisioning/dashboards
```

---

## 알림 구조 선택

| 선택 | 설명 |
|------|------|
| Alertmanager만 사용 | **권장** — 경보 라우팅 전담 |
| Grafana Alert도 사용 | 패널 → Alert 탭에서 추가 가능 |

Alertmanager를 이미 사용하는 구조라면 Grafana는 시각화 전용으로 유지 권장

## 관련 개념
- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - 데이터 소스
- [[02 - Knowledge/Computing/Backend/Monitoring/PromQL\|PromQL]] - 패널 쿼리 문법
- [[02 - Knowledge/Computing/Backend/Monitoring/Alertmanager\|Alertmanager]] - 경보 발송
