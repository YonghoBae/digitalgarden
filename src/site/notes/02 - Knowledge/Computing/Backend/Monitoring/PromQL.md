---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/PromQL/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# PromQL

> Prometheus 쿼리 언어 — 메트릭 조회/집계/계산

## 핵심 개념

| 개념 | 설명 |
|------|------|
| **메트릭 타입** | Counter(누적), Gauge(현재값), Histogram/Summary(분포) |
| **선택자** | `metric{label="v", label!=...}` |
| **범위 벡터** | `metric[5m]` (최근 5분 구간) |
| **증분/속도** | `rate()`, `irate()`, `increase()` |
| **집계** | `sum()`, `avg()`, `max()`, `min()` + `by(...)` |
| **퍼센타일** | `histogram_quantile(0.95, ...)` |

---

## 선택자 & 필터

```promql
# 라벨로 필터
node_cpu_seconds_total{mode="idle", instance="your-host:9100"}

# 정규식 매칭
node_network_receive_bytes_total{device=~"enp.*|eth.*"}

# 라벨 제외
node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"}
```

---

## CPU · 메모리 · 디스크 (node-exporter)

### CPU 사용률 (%)

```promql
# 인스턴스별 평균
100 - (avg by (instance)(
  rate(node_cpu_seconds_total{mode="idle"}[5m])
) * 100)
```

### 메모리 사용률 (%)

```promql
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100
```

### 디스크 사용률 (%) & 남은 용량

```promql
# 사용률
(1 - (node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"}
      / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"})) * 100

# 남은 용량 (GiB)
node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / 1024^3
```

### 디스크 I/O, 네트워크

```promql
rate(node_disk_read_bytes_total[5m])
rate(node_disk_written_bytes_total[5m])

sum by(instance)(rate(node_network_receive_bytes_total{device!~"lo"}[5m]))
sum by(instance)(rate(node_network_transmit_bytes_total{device!~"lo"}[5m]))
```

---

## 컨테이너 (cAdvisor)

> 환경마다 라벨명이 다를 수 있음. 먼저 `container!=""` 로 라벨 이름 파악 후 적용

```promql
# CPU 사용률 (% of 1 CPU)
sum by (container)(
  rate(container_cpu_usage_seconds_total{container!=""}[5m])
) * 100

# 메모리 (MiB)
container_memory_working_set_bytes{container!=""} / 1024^2

# 네트워크 수신
sum by (container)(rate(container_network_receive_bytes_total{container!=""}[5m]))
```

---

## 프로브(가용성) — blackbox-exporter

```promql
# 성공(1)/실패(0)
probe_success{instance="api"}

# 응답 지연 (초)
probe_duration_seconds{instance="api"}

# HTTP 상태 코드
probe_http_status_code{instance="api"}

# 인증서 만료까지 남은 일수
(probe_ssl_earliest_cert_expiry - time()) / 86400
```

---

## 앱 내부 지표 (prom-client)

```promql
# RPS (초당 요청 수)
sum(rate(http_requests_total[1m]))

# 에러율 (%)
100 * sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m]))

# 레이턴시 P95 (Histogram)
histogram_quantile(
  0.95,
  sum by (le)(rate(http_request_duration_seconds_bucket[5m]))
)
```

---

## MySQL / Redis

```promql
# MySQL 연결 수
mysql_global_status_threads_connected

# MySQL QPS
rate(mysql_global_status_questions[1m])

# Redis 연결 클라이언트
redis_connected_clients

# Redis 메모리 (MiB)
redis_memory_used_bytes / 1024^2
```

---

## 집계 · 그룹핑

```promql
# 서비스별 RPS
sum by (service)(rate(http_requests_total[1m]))

# 최근 1시간 최대값
max_over_time(node_load1[1h])

# 24시간 증가량
increase(http_requests_total[24h])

# 특정 메트릭 부재 감지
absent(up{job="host"})
```

---

## 자주 쓰는 스니펫

| 목적 | 쿼리 |
|------|------|
| 호스트 CPU % | `100 - (avg by (instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` |
| 호스트 메모리 % | `(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100` |
| 디스크 사용률 % | `(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100` |
| 컨테이너 CPU % | `sum by (container)(rate(container_cpu_usage_seconds_total{container!=""}[5m])) * 100` |
| API 가용성 | `probe_success{instance="api"}` |
| API P95 레이턴시 | `histogram_quantile(0.95, sum by (le)(rate(http_request_duration_seconds_bucket[5m])))` |

---

## 실전 팁

- **Counter는 반드시 `rate()`/`increase()`와 함께** 사용 (누적치를 속도로 변환)
- **범위 윈도우**: 1m(민감) / 5m(안정) / 15m(완만) — 대시보드 vs 알람 목적에 따라 조절
- **Blackbox**는 `metrics_path: /probe` + `relabel_configs` 패턴 필수

## 관련 개념
- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - 수집 설정
- [[02 - Knowledge/Computing/Backend/Monitoring/Grafana\|Grafana]] - 대시보드 시각화
- [[02 - Knowledge/Computing/Backend/Monitoring/Alertmanager\|Alertmanager]] - 경보 규칙
