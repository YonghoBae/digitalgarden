---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Recording Rules/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Recording Rules

> PromQL 표현식을 미리 계산해 새 메트릭으로 저장 — 스파이크 제거 및 복잡한 쿼리 최적화

## 목적

| 용도 | 설명 |
|------|------|
| **노이즈 제거** | 순간 스파이크를 평균화해 체감과 일치하는 지표로 변환 |
| **쿼리 최적화** | 반복 계산이 많은 복잡한 PromQL을 미리 저장 |
| **대시보드 성능** | 매번 계산하지 않고 저장된 값 조회 |

---

## 기본 구조 (rules.yml)

```yaml
groups:
  - name: recording_rules
    interval: 1m          # 평가 주기 (prometheus.yml의 evaluation_interval 기본값 사용)
    rules:
      - record: job:http_requests:rate5m
        expr: sum by (job)(rate(http_requests_total[5m]))
```

> `record` 이름 규칙: `level:metric:operations` 형식 권장 (예: `job:http_requests:rate5m`)

---

## 실전 예시 — 원격데스크탑 지연 모니터링

60fps 환경에서 프레임 1개 처리 예산은 **16.666ms**. 이 값을 초과하면 체감 지연 발생.

```yaml
groups:
  - name: remote_desktop
    interval: 1m
    rules:
      # 프레임 처리 지연 — 16ms 예산 대비 비율 (avg로 스파이크 제거)
      - record: apollo:frame_latency:budget_ratio1m
        expr: |
          avg_over_time(apollo_frame_processing_latency_avg[1m]) / 16.666

      # 입력 지연 — 1분 평균
      - record: apollo:input_latency:avg1m
        expr: avg_over_time(apollo_input_latency_avg[1m])

      # 입력 큐 깊이 — 1분 평균
      - record: apollo:input_queue:avg1m
        expr: avg_over_time(apollo_input_queue_depth_avg[1m])
```

> max 값을 기준으로 잡으면 200ms 같은 이상값이 나올 수 있음 — avg 사용 권장

---

## prometheus.yml 연결

```yaml
rule_files:
  - /etc/prometheus/rules.yml    # Recording Rules + Alert Rules 동일 파일 사용 가능
```

---

## Alert Rule과의 차이

| 구분 | Recording Rule | Alert Rule |
|------|---------------|------------|
| 목적 | 새 메트릭 생성 | 조건 만족 시 경보 발생 |
| 결과 | TSDB에 저장 | Alertmanager로 전달 |
| `record` 키 | 사용 | 사용 안 함 |
| `alert` 키 | 사용 안 함 | 사용 |

```yaml
# Alert Rule 비교
groups:
  - name: alerts
    rules:
      - alert: HighFrameLatency
        expr: apollo:frame_latency:budget_ratio1m > 1.0
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "프레임 처리 지연 16ms 초과"
```

---

## 관련 개념

- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - rule_files 등록
- [[02 - Knowledge/Computing/Backend/Monitoring/PromQL\|PromQL]] - expr 문법
- [[02 - Knowledge/Computing/Backend/Monitoring/Alertmanager\|Alertmanager]] - Alert Rule 경보 수신
