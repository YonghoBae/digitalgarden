---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/_Monitoring/","tags":["type/hub","context/studies","theme/backend","status/active"]}
---

# Monitoring

> Prometheus + Grafana + Alertmanager 기반 모니터링 스택

## 전체 흐름

```
수집 (Exporter) → 저장/조회 (Prometheus + PromQL) → 시각화 (Grafana)
                                                   → 경보 (Alertmanager → Discord/Webhook)
```

## 핵심 주제

- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - 메트릭 수집, TSDB, Alert Rule
- [[02 - Knowledge/Computing/Backend/Monitoring/PromQL\|PromQL]] - 쿼리 문법, 주요 함수, 실전 쿼리
- [[02 - Knowledge/Computing/Backend/Monitoring/Recording Rules\|Recording Rules]] - 메트릭 사전 계산, 스파이크 제거
- [[02 - Knowledge/Computing/Backend/Monitoring/Grafana\|Grafana]] - 대시보드, 패널, 변수
- [[02 - Knowledge/Computing/Backend/Monitoring/Alertmanager\|Alertmanager]] - 경보 라우팅, Receiver, Inhibit
- [[02 - Knowledge/Computing/Backend/Monitoring/Exporter\|Exporter]] - node-exporter, cAdvisor, blackbox, mysqld, redis
- [[02 - Knowledge/Computing/Backend/Monitoring/Loki\|Loki]] - 로그 수집/저장 (Promtail → Loki → Grafana)

## 주요 포트

| 서비스 | 포트 |
|--------|------|
| Prometheus | 9090 |
| Grafana | 3000 |
| Alertmanager | 9093 |
| node-exporter | 9100 |
| cAdvisor | 8080 |
| blackbox-exporter | 9115 |
| Loki | 3100 |

## 관련 개념
- [[02 - Knowledge/Computing/Cloud Computing/_CloudComputing\|Cloud Computing]] - 인프라 기반
