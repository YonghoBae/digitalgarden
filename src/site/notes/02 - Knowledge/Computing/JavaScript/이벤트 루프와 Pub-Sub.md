---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/JavaScript/이벤트 루프와 Pub-Sub/","tags":["type/knowledge","context/studies","theme/javascript"]}
---

# 이벤트 루프와 Pub-Sub 패턴

> Node.js/JavaScript의 비동기 처리 핵심 구조와 컴포넌트 간 결합도를 낮추는 Pub-Sub 패턴.

---

## 프로세스 vs 스레드

| 구분 | 프로세스 (Process) | 스레드 (Thread) |
|------|-------------------|----------------|
| 정의 | 실행 중인 프로그램, 자원 할당 단위 | 프로세스 내 실행 흐름 단위 |
| 메모리 | 독립적인 영역 할당 | 프로세스 메모리 공유 (Stack 제외) |
| 자원 공유 | 공유 안 함 (IPC 필요) | 공유 |
| 오버헤드 | 생성·컨텍스트 스위칭 비용 큼 | 비용 작음 |
| 안정성 | 프로세스 간 격리됨 | 한 스레드 오류가 전체에 영향 |

---

## Race Condition과 동기화

**Race Condition**: 여러 스레드가 공유 자원에 동시 접근할 때 실행 순서에 따라 결과가 달라지는 상황.

| 동기화 기법 | 설명 |
|------------|------|
| **Lock** | 임계 영역 진입 전 락 획득, 나올 때 해제 |
| **Mutex** | 오직 하나의 스레드만 임계 영역 진입. 락 획득 스레드만 해제 가능 |
| **Semaphore** | N개 스레드 동시 접근 허용. Binary(=1) → Mutex와 유사, 단 다른 스레드도 해제 가능 |

---

## 비동기 이벤트 매니저 구조

```
[비동기 작업 완료]
        ↓
[이벤트 큐 (Event Queue)]
        ↓  (콜 스택이 비었을 때)
[이벤트 루프 (Event Loop)]
        ↓
[콜백 / 이벤트 핸들러 실행]
```

- **이벤트 루프**: 끊임없이 이벤트 큐를 확인
- **이벤트 큐**: 비동기 작업의 콜백이 등록되는 대기열
- **이벤트 핸들러**: 이벤트 발생 시 실행되는 함수

---

## Publisher-Subscriber 패턴 (Pub/Sub)

발행자(Publisher)와 구독자(Subscriber)의 **직접 결합을 제거**하는 패턴. 옵저버 패턴과 유사하나 중개자(브로커)가 존재.

```
Publisher  →  [Event Channel / Broker]  →  Subscriber(s)
```

| 구성 요소 | 역할 |
|-----------|------|
| **Publisher** | 이벤트 발행. 누가 받는지 모름 |
| **Subscriber** | 이벤트 구독. 누가 보냈는지 모름 |
| **Broker** | 발행자의 메시지를 구독자들에게 전달 |

**사용 시기**: 여러 컴포넌트가 서로의 상태 변화를 알아야 하지만 직접 결합하고 싶지 않을 때. 실시간 알림, 채팅, 분산 시스템 동기화.

---

## Node.js EventEmitter

Pub/Sub 패턴을 구현한 Node.js 내장 클래스.

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();

// 구독 (on)
emitter.on('data', (payload) => {
    console.log('수신:', payload);
});

// 발행 (emit)
emitter.emit('data', { message: 'hello' });

// 한 번만 수신
emitter.once('done', () => console.log('완료'));

// 구독 해제
emitter.removeListener('data', handler);
```

> `Socket` 객체와 `Worker`도 `EventEmitter`를 상속받아 구현됨.

---

## 특수 큐 종류

| 큐 | 설명 | 사용 예 |
|----|------|---------|
| **SyncQueue** | 크기 고정. 생산자/소비자 속도 조절 (가득 차면 대기) | 멀티 스레드 Bounded Buffer |
| **AsyncQueue** | Non-blocking. `await`로 데이터 대기 | 이벤트 루프 기반 비동기 처리 |
| **DelayQueue** | 아이템마다 만료 시간 지정 | 캐시 만료, 지연 스케줄링 |

---

## 참고

- [[02 - Knowledge/Computing/JavaScript/비동기 프로그래밍\|비동기 프로그래밍]]
- 학습 출처: 네이버 부스트캠프 챌린지 Day9
