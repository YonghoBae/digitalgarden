---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/CS/Security/스트림 암호와 RC4/","tags":["type/study","context/studies","theme/cs","status/completed"]}
---

# 스트림 암호와 RC4

> 연속적 처리 방식의 암호화 — 의사 난수 키스트림 생성

## 스트림 암호

- 입력 요소를 연속적으로 처리 (블록 단위 X)
- 의사 난수 비트 생성기([[02 - Knowledge/Computing/CS/Security/난수와 키 분배\|난수와 키 분배]])로 키스트림 생성
- 평문 바이트 XOR 키스트림 → 암호문 (1바이트씩)

**블록 암호 대비 특징**
- 적은 코드 사용
- 블록 암호보다 빠름
- 키 재사용 불가 (같은 키스트림으로 두 번 XOR = 원문 노출)

### 설계 고려사항

| 조건 | 이유 |
|------|------|
| 암호화 시퀀스 주기가 커야 함 | 주기 짧으면 패턴 반복 노출 |
| 키스트림이 난수 속성과 유사 | 예측 불가능성 확보 |
| 충분한 키 길이 | 전수공격 방지 |

---

## RC4 (Rivest Cipher 4)

- 셔플링(Shuffling) 기법 기반 스트림 암호
- 바이트 단위 처리, 가변 길이 키 (1~256byte)

### S 배열 초기화

```
# 초기화 (256바이트 배열)
for i = 0 to 255:
    S[i] = i                     # 0~255으로 초기화
    T[i] = key[i mod KeyLen]     # 키로 T 초기화

j = 0
# S 배열 섞기
for i = 0 to 255:
    j = (j + S[i] + T[i]) mod 256
    Swap(S[i], S[j])
```

### 키스트림 생성

```
i = j = 0
for each plaintext byte:
    i = (i + 1) mod 256
    j = (j + S[i]) mod 256
    Swap(S[i], S[j])

    t = (S[i] + S[j]) mod 256
    keystreamByte = S[t]
    output = plaintext XOR keystreamByte
```

**동작 원리**
1. 비밀키로 256바이트 배열을 무작위하게 섞음
2. 매 바이트마다 두 포인터(i, j)를 이동하며 t 계산
3. S[t] 값으로 평문 1바이트씩 XOR → 암호화

---

## CFB 모드와의 관계

블록 암호(AES/DES)를 이용해 스트림 암호처럼 동작 가능

```
CFB 모드: 블록 암호 → 암호화 출력 → XOR → 스트림 방식 처리
OFB 모드: 피드백 경로가 평문 XOR 이전 → 더 순수한 스트림 구조
```

## 관련 개념
- [[02 - Knowledge/Computing/CS/Security/블록 암호 운용 모드\|블록 암호 운용 모드]] - CFB/OFB (스트림 방식 운용)
- [[02 - Knowledge/Computing/CS/Security/난수와 키 분배\|난수와 키 분배]] - 의사 난수 생성기
- [[02 - Knowledge/Computing/CS/Security/대칭 암호화\|대칭 암호화]] - 대칭 암호화 전체
