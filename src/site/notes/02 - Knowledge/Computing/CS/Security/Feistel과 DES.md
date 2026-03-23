---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/Security/Feistel과 DES/","tags":["type/study","context/studies","theme/cs","status/completed"]}
---

# Feistel과 DES

> DES의 핵심 구조 — Feistel 네트워크 기반 블록 암호

## Feistel 구조

체인 형태로 연결된 라운드 구조 — 암호화와 복호화가 동일한 구조

```
입력(64bit)
 └─ L0(32bit), R0(32bit) 분리
      ↓ 1라운드
      L1 = R0
      R1 = L0 XOR F(R0, K1)
      ↓ ... 16라운드 ...
출력(64bit)
```

### 특성

| 특성 | 설명 |
|------|------|
| 라운드 수 조정 가능 | DES = 16라운드 |
| 각 라운드 다른 서브키 | 16개의 보조키 생성 |
| 라운드 함수 F 교체 가능 | 다양한 함수 적용 |
| 복호화 = 암호화 역순 | 동일 구조로 복호화 가능 |

---

## DES 라운드 함수 F (Feistel 함수)

```
입력: Right(32bit) + 보조키(48bit)

1. Initial Permutation (IP)     : 64bit → 64bit (순서 변경)
2. Expansion Permutation        : 32bit → 48bit (보조키 길이 맞춤)
3. XOR 연산                     : 확장된 Right(48bit) XOR 보조키(48bit)
4. S-Box 치환                   : 48bit → 32bit
   - 6bit×8개 토막으로 분리
   - 각 토막: 1·6번째 bit → Row, 2·3·4·5번째 bit → Column
   - 각 S-Box에서 4bit 출력
5. P-Box (순열)                 : 32bit → 32bit
6. XOR with Left                : Left(32bit) XOR 결과(32bit) → 다음 Right
```

**S-Box 예시**
```
입력: 011011
Row = 0y1 → 01 = 1행
Col = x101x → 1101 = 13열
출력: S(1)[1][13] → 4bit 값
```

---

## Feistel 보조키 생성 (DES)

```
원본 키: 64bit (56bit 유효 + 8bit 에러 정정)
  ↓ PC-1 전치 (56bit 선택)
  ↓ 좌우 28bit 분리
  ↓ 16회 순환 이동
  ↓ PC-2 전치 (48bit 선택)
  → 16개의 보조키 (각 48bit)
```

| 단계 | 설명 |
|------|------|
| 에러 정정 비트 | 8bit마다 1bit → 총 8bit 제거 → 56bit |
| PC-1 | 56bit 선택 순열 |
| 순환 이동 | 라운드마다 1~2bit 좌측 이동 |
| PC-2 | 56bit → 48bit 선택 순열 |

---

## 블록 암호 설계 파라미터

| 파라미터 | 설명 |
|----------|------|
| 블록 크기 | 크면 보안↑, 성능↓ |
| 키 크기 | 크면 무차별 대입 저항↑ |
| 라운드 수 | 많으면 보안↑ |
| 서브키 생성 알고리즘 | 복잡할수록 분석 어려움 |
| 라운드 함수 | 비선형성↑ → 해독 어려움 |

## 관련 개념
- [[02 - Knowledge/Computing/CS/Security/대칭 암호화\|대칭 암호화]] - DES/3DES/AES 비교
- [[02 - Knowledge/Computing/CS/Security/블록 암호 운용 모드\|블록 암호 운용 모드]] - ECB/CBC/CFB/CTR
