---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/JavaScript/V8 메모리와 ArrayBuffer/","tags":["type/study","context/studies","theme/javascript","status/completed"]}
---

# V8 메모리와 ArrayBuffer

> Node.js V8 엔진의 메모리 구조와 JavaScript 저수준 이진 데이터 처리 API.

---

## 프로세스 메모리 구조

| 영역 | 역할 | 특징 |
|------|------|------|
| **TEXT** | 함수 코드, 상수 | 컴파일 시 크기 결정, 읽기 전용, 공유 가능 |
| **GVAR/BSS** | 전역/정적 변수 | 특정 초기값 → GVAR, 0 초기화 → BSS |
| **HEAP** | 동적 할당 (`new`, `malloc`) | 런타임 크기 결정, 낮은→높은 주소 방향 |
| **STACK** | 지역변수, 매개변수, 반환값 | 함수 호출 시 할당/종료 시 자동 해제, 높은→낮은 주소 방향 |

> Stack overflow: 재귀 호출 무한 반복 시 스택 공간 소진

---

## V8 엔진 힙 구조 (Resident Set)

V8은 **세대별 가비지 컬렉션(Generational GC)**으로 힙을 관리:

| 공간 | 대상 | GC 방식 |
|------|------|---------|
| **New Space** (Young Gen) | 새로 생성된 객체 | Scavenge (빠르고 빈번) |
| **Old Space** (Old Gen) | New Space에서 살아남은 객체 | Mark-Sweep / Mark-Compact (덜 빈번, 철저) |

### 분석 방법

```js
// 현재 힙 사용량
process.memoryUsage()
// { heapTotal, heapUsed, rss, external }

// Chrome DevTools: node --inspect script.js
// → chrome://inspect → Memory 탭 → 힙 스냅샷 비교로 메모리 누수 탐지
```

---

## ArrayBuffer / DataView

### ArrayBuffer

```js
// 8바이트 순수 메모리 공간 할당 (읽기/쓰기 불가 — DataView 필요)
const buffer = new ArrayBuffer(8);
console.log(buffer.byteLength); // 8
```

- 연속된 바이트로 이루어진 **날것의 메모리 공간**
- 고정 크기, 직접 조작 불가

### DataView

```js
const view = new DataView(buffer);

// 쓰기
view.setUint8(0, 255);    // 0번지에 1바이트 정수
view.setInt32(1, 1000);   // 1번지에 4바이트 정수

// 읽기
view.getUint8(0);   // 255
view.getInt32(1);   // 1000
```

- ArrayBuffer의 **특정 위치를 원하는 타입으로 해석하는 인터페이스**
- 데이터 타입(정수/실수)과 Endianness 상관없이 자유로운 조작

> **관계**: ArrayBuffer = 메모리 공간, DataView = 해석 도구
> 두 개가 함께 있어야 의미 있음

---

## 관련 개념
- [[02 - Knowledge/Computing/JavaScript/이벤트 루프와 Pub-Sub\|이벤트 루프와 Pub-Sub]] — V8 이벤트 루프
- [[02 - Knowledge/Computing/JavaScript/비동기 프로그래밍\|비동기 프로그래밍]] — Node.js 비동기 I/O
