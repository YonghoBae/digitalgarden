---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Backend/Monitoring/Exporter/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

# Exporter

> Prometheus가 Pull할 수 있도록 메트릭을 `/metrics` 엔드포인트로 노출하는 컴포넌트

## 공통 컨벤션

- 네트워크: `monitoring_net`
- Prometheus `prometheus.yml`에 서비스명:포트로 타깃 등록
- 검증: `curl http://<container>:<port>/metrics`

---

## 1. node-exporter (호스트 OS)

호스트의 CPU/RAM/디스크/온도를 `/metrics`에 노출

```yaml
services:
  node-exporter:
    image: prom/node-exporter:v1.8.2
    container_name: node-exporter
    command:
      - '--path.rootfs=/host'
    pid: host
    volumes:
      - '/:/host:ro,rslave'
    restart: unless-stopped
    networks:
      - monitoring_net
```

Prometheus 타깃:
```yaml
- job_name: 'host'
  static_configs:
    - targets: ['node-exporter:9100']
```

- 포트: `9100`
- 커스텀 메트릭: `--collector.textfile.directory` 옵션으로 텍스트파일 컬렉터 추가 가능

---

## 2. cAdvisor (컨테이너)

도커 컨테이너별 CPU/메모리/재시작 수를 `/metrics` 노출

```yaml
services:
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.49.2
    container_name: cadvisor
    privileged: true
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    restart: unless-stopped
    networks:
      - monitoring_net
```

Prometheus 타깃:
```yaml
- job_name: 'containers'
  static_configs:
    - targets: ['cadvisor:8080']
```

- 포트: `8080`
- rootless 환경에서 권한 문제 시 `privileged: true` 또는 마운트 경로 점검

---

## 3. blackbox-exporter (HTTP/TCP/ICMP 프로브)

프로브 대상을 파라미터로 받아 가용성 체크

```yaml
services:
  blackbox:
    image: prom/blackbox-exporter:v0.25.0
    container_name: blackbox
    volumes:
      - ./blackbox.yml:/etc/blackbox_exporter/config.yml:ro
    command:
      - '--config.file=/etc/blackbox_exporter/config.yml'
    restart: unless-stopped
    networks:
      - monitoring_net
```

`blackbox.yml`:
```yaml
modules:
  http_2xx:
    prober: http
    timeout: 10s
    http:
      method: GET
      valid_status_codes: [200, 302]
      preferred_ip_protocol: "ip4"

  tcp_connect:
    prober: tcp
    timeout: 5s
```

Prometheus 타깃 (HTTP 헬스체크):
```yaml
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
```

- 포트: `9115`
- `metrics_path: /probe` + `relabel_configs` 패턴 필수

---

## 4. mysqld_exporter (선택)

MySQL 연결/쿼리율/슬로우쿼리 등

```yaml
services:
  mysqld-exporter:
    image: prom/mysqld-exporter:v0.15.1
    container_name: mysqld-exporter
    environment:
      DATA_SOURCE_NAME: 'exporter:password@(db:3306)/'
    restart: unless-stopped
    networks:
      - monitoring_net
```

MySQL 최소 권한 설정:
```sql
CREATE USER 'exporter'@'%' IDENTIFIED BY 'password';
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
FLUSH PRIVILEGES;
```

- 포트: `9104`
- 보안: 환경변수 대신 Docker secrets 권장

---

## 5. redis_exporter (선택)

Redis 메모리/키/evicted keys/AOF/RDB 상태

```yaml
services:
  redis-exporter:
    image: oliver006/redis_exporter:v1.62.0
    container_name: redis-exporter
    environment:
      REDIS_ADDR: 'redis://redis:6379'
      # REDIS_PASSWORD: 'yourpass'
    restart: unless-stopped
    networks:
      - monitoring_net
```

- 포트: `9121`

---

## 전체 compose 스니펫 (필요한 것만 선택)

```yaml
services:
  node-exporter:
    image: prom/node-exporter:v1.8.2
    command: ['--path.rootfs=/host']
    pid: host
    volumes: ['/:/host:ro,rslave']
    networks: [monitoring_net]
    restart: unless-stopped

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:v0.49.2
    privileged: true
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    networks: [monitoring_net]
    restart: unless-stopped

  blackbox:
    image: prom/blackbox-exporter:v0.25.0
    volumes:
      - ./blackbox.yml:/etc/blackbox_exporter/config.yml:ro
    networks: [monitoring_net]
    restart: unless-stopped

  mysqld-exporter:
    image: prom/mysqld-exporter:v0.15.1
    environment:
      DATA_SOURCE_NAME: 'exporter:password@(db:3306)/'
    networks: [monitoring_net]
    restart: unless-stopped

  redis-exporter:
    image: oliver006/redis_exporter:v1.62.0
    environment:
      REDIS_ADDR: 'redis://redis:6379'
    networks: [monitoring_net]
    restart: unless-stopped

networks:
  monitoring_net:
    driver: bridge
```

---

## 보안 팁

- 외부 포트 노출 최소화: Prometheus만 내부 네트워크에서 접근
- `node-exporter`: 호스트 마운트 read-only 유지
- `mysqld_exporter`/`redis_exporter`: 최소 권한 계정 사용

## 관련 개념
- [[02 - Knowledge/Computing/Backend/Monitoring/Prometheus\|Prometheus]] - 수집 타깃 설정
- [[02 - Knowledge/Computing/Backend/Monitoring/PromQL\|PromQL]] - 메트릭 조회
- [[02 - Knowledge/Computing/Backend/Monitoring/_Monitoring\|Monitoring]] - 전체 흐름
