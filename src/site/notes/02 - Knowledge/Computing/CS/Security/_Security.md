---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/Security/_Security/","tags":["type/hub","context/studies","theme/cs","status/active"]}
---

# Security

> 정보보안 — 암호화, 해시, 네트워크 공격과 방어

## 전체 흐름

```
보안 목표 (CIA)
  ├─ 기밀성 → 암호화 (대칭/비대칭)
  ├─ 무결성 → 해시 함수, 전자서명
  └─ 가용성 → DoS 방어, 방화벽

공격
  ├─ 네트워크 공격 (DoS, APT, 스피어피싱)
  └─ 악성 소프트웨어 (바이러스, 웜, 트로이목마)

방어
  ├─ 암호화 (대칭키, 블록/스트림 암호)
  └─ 탐지/차단 (IDS, IPS, 방화벽, 허니팟)
```

## 핵심 주제

### 암호화
- [[02 - Knowledge/Computing/CS/Security/보안 기초\|보안 기초]] - CIA 3요소, 공격 분류, 보안 전략
- [[02 - Knowledge/Computing/CS/Security/대칭 암호화\|대칭 암호화]] - DES, 3DES, AES, 블록/스트림 암호
- [[02 - Knowledge/Computing/CS/Security/Feistel과 DES\|Feistel과 DES]] - Feistel 구조, 라운드 함수, 보조키 생성
- [[02 - Knowledge/Computing/CS/Security/블록 암호 운용 모드\|블록 암호 운용 모드]] - ECB, CBC, CFB, OFB, CTR
- [[02 - Knowledge/Computing/CS/Security/스트림 암호와 RC4\|스트림 암호와 RC4]] - RC4 셔플링, 키스트림 생성
- [[02 - Knowledge/Computing/CS/Security/해시 함수\|해시 함수]] - SHA, HMAC, 메시지 인증
- [[02 - Knowledge/Computing/CS/Security/난수와 키 분배\|난수와 키 분배]] - PRNG, KDC, 링크/종단간 암호화

### 공격
- [[02 - Knowledge/Computing/CS/Security/네트워크 공격\|네트워크 공격]] - DoS/DDoS, APT, 스피어피싱, 익스플로잇 킷
- [[02 - Knowledge/Computing/CS/Security/악성 소프트웨어\|악성 소프트웨어]] - 바이러스, 웜, 트로이목마, 랜섬웨어, APT

### 방어
- [[02 - Knowledge/Computing/CS/Security/침입탐지와 방어\|침입탐지와 방어]] - IDS/IPS, 방화벽, 허니팟, SNORT

## 주요 알고리즘 비교

| 알고리즘 | 유형 | 키 크기 | 블록 크기 |
|----------|------|---------|-----------|
| DES | 대칭 블록 | 56bit | 64bit |
| 3DES | 대칭 블록 | 168bit | 64bit |
| AES | 대칭 블록 | 128/192/256bit | 128bit |
| RC4 | 대칭 스트림 | 가변 | - |
| SHA-256 | 해시 | - | 512bit 블록 → 256bit 출력 |
| SHA-512 | 해시 | - | 1024bit 블록 → 512bit 출력 |

## 관련 개념
- [[02 - Knowledge/Computing/CS/Network/_Network\|Network]] - OSI 계층, TCP/IP
- [[02 - Knowledge/Computing/CS/OS/_OS\|OS]] - 프로세스, 메모리 보호
