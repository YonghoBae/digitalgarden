---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Alertmanager/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Alertmanager

> Prometheus가 감지한 경보를 "어디로, 어떻게 보낼지" 결정하는 라우팅 시스템

## 핵심 개념

| 요소 | 설명 |
|------|------|
| **route** | 알림 라우팅 규칙 |
| **receivers** | 실제 발송 대상 (webhook, Discord, Slack, email 등) |
| **grouping** | 동일 경보 여러 개 → 하나로 묶어서 전송 |
| **inhibit** | 특정 조건일 때 다른 경보 억제 (예: API DOWN → 5xx 알람 묵음) |
| **template** | 메시지 커스터마이징 (Go 템플릿 문법) |

---

## 기본 구조

```yaml
global:
  resolve_timeout: 5m   # 경보 해결 후 "Resolved" 알림 보내기까지 시간

route:
  receiver: "default"
  group_wait: 30s       # 최초 경보 발생 후 그룹핑 대기 시간
  group_interval: 5m    # 기존 그룹에 새 경보 추가 시 전송 주기
  repeat_interval: 3h   # 같은 경보 반복 전송 주기

receivers:
  - name: "default"
    webhook_configs:
      - url: "http://example.com/alert"
```

---

## Discord + Webhook + Severity 라우팅

```yaml
global:
  resolve_timeout: 5m

route:
  receiver: "webhook-handler"
  group_by: ["alertname", "instance"]
  group_wait: 30s
  group_interval: 3m
  repeat_interval: 2h

  routes:
    - match:
        severity: critical
      receiver: "discord-critical"

    - match:
        severity: warning
      receiver: "discord-warning"

receivers:
  # Webhook → Spring Boot 등 처리 서버
  - name: "webhook-handler"
    webhook_configs:
      - url: "http://spring-app:8080/alert"
        send_resolved: true

  # Discord (Critical)
  - name: "discord-critical"
    discord_configs:
      - webhook_url: "https://discord.com/api/webhooks/XXXXX"

  # Discord (Warning)
  - name: "discord-warning"
    discord_configs:
      - webhook_url: "https://discord.com/api/webhooks/YYYYY"
```

> `severity` 라벨은 Alert Rule에서 정의한 값 기준으로 분기

---

## Webhook 수신 JSON 형식

Alertmanager가 보내는 페이로드:

```json
{
  "version": "4",
  "status": "firing",
  "alerts": [
    {
      "labels": {
        "alertname": "HostHighCpu",
        "severity": "warning",
        "instance": "home-server:9100"
      },
      "annotations": {
        "summary": "High CPU usage",
        "description": "CPU usage above 80%..."
      }
    }
  ]
}
```

---

## Inhibit Rule (경보 억제)

상위 에러 발생 시 하위 경보 자동 억제

```yaml
inhibit_rules:
  - source_match:
      alertname: "ApiDown"
    target_match:
      alertname: "High5xxRate"
    equal: ["instance"]
```

---

## 설정 검증

```bash
# 설정 파일 문법 검사
amtool check-config alertmanager.yml

# 디버그 모드 실행
alertmanager --config.file=alertmanager.yml --log.level=debug
```

## 관련 개념
- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - Alert Rule 설정 (rules.yml)
- [[02 - Knowledge/Computing/Backend/Monitoring/Grafana\|Grafana]] - 시각화 연동
- [[02 - Knowledge/Computing/Backend/Monitoring/_Monitoring\|Monitoring]] - 전체 경보 흐름
