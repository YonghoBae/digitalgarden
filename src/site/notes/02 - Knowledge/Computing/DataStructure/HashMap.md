---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/DataStructure/HashMap/","tags":["type/study","context/studies","theme/data-structure","status/completed"]}
---

# HashMap

> Map + 해시 함수 + 충돌 처리 → Key-Value 고속 조회

## Map

키-값 쌍으로 데이터를 저장하는 자료구조

| 특징 | 설명 |
|------|------|
| 모든 타입을 키로 사용 가능 | 문자열, 숫자, 객체, 함수 등 |
| 삽입 순서 유지 | 일반 Object와 차이 |
| 키 중복 불허 | set() 시 덮어씀 |
| 크기 확인 | `.size` 프로퍼티 |

### JavaScript Map API

```javascript
const map = new Map();

map.set('name', '홍길동');   // 추가
map.get('name');             // 조회
map.has('name');             // 존재 확인 → boolean
map.delete('name');          // 삭제
map.clear();                 // 전체 초기화
map.size;                    // 크기

// 순회
for (const [key, value] of map) {
    console.log(key, value);
}
```

---

## 해시 함수 (Hash Function)

임의 입력값을 고정 범위의 정수(해시값)로 변환

### 조건
- **Deterministic**: 같은 입력 → 항상 같은 출력
- **균등 분포**: 해시값이 고르게 퍼져야 충돌 최소화
- **빠른 계산**: 주로 비트 연산 사용

### 구현 예시 (JavaScript)

```javascript
_hash(key) {
    let hash = 0;
    const PRIME = 31;
    for (let i = 0; i < key.length; i++) {
        const code = key.charCodeAt(i);
        hash ^= (code << (i % 8));  // 위치 기반 시프트 + XOR
        hash = (hash * PRIME) >>> 0; // 소수 곱 + 32비트 정수 변환
    }
    return hash % this.buckets.length;
}
```

**설계 포인트**
- `i % 8` 시프트: 위치가 다른 문자는 다른 해시값 (`ab` ≠ `ba`)
- `* PRIME(31)`: 해시값 분산 향상 (Java, JavaScript 관례)
- `>>> 0`: 음수/오버플로우 방지 (32비트 부호 없는 정수)
- 버킷 크기는 소수(예: 997) 사용 → 균등 분포에 유리

---

## HashMap 구조

```
버킷 배열 (bucket array)
  [0] → [(key1, val1), (key2, val2)]  ← 충돌 시 체이닝
  [1] → [(key3, val3)]
  [2] → []
  ...
```

### 핵심 구성 요소

| 구성 요소 | 설명 |
|-----------|------|
| 해시 함수 | 키 → 버킷 인덱스 변환 |
| 버킷 배열 | 실제 데이터 저장 (소수 크기 권장) |
| 충돌 처리 | 동일 해시값 → 체이닝(Chaining) or 개방 주소법 |
| size 변수 | 저장된 항목 개수 추적 |

### 충돌 처리: 체이닝 (Chaining)

각 버킷을 연결 리스트(또는 배열)로 유지 → 같은 해시값이면 같은 버킷에 추가

```javascript
class HashMap {
    constructor() {
        this.buckets = new Array(997).fill(null).map(() => []);
        this._size = 0;
    }

    put(key, value) {
        const index = this._hash(key);
        const bucket = this.buckets[index];
        const existing = bucket.find(([k]) => k === key);
        if (existing) {
            existing[1] = value;  // 업데이트
        } else {
            bucket.push([key, value]);
            this._size++;
        }
    }

    get(key) {
        const index = this._hash(key);
        const pair = this.buckets[index].find(([k]) => k === key);
        return pair ? pair[1] : undefined;
    }

    remove(key) {
        const index = this._hash(key);
        const bucket = this.buckets[index];
        const i = bucket.findIndex(([k]) => k === key);
        if (i > -1) {
            bucket.splice(i, 1);
            this._size--;
        }
    }

    size() { return this._size; }
    isEmpty() { return this._size === 0; }
    containsKey(key) { return this.get(key) !== undefined; }
    keys() { return this.buckets.flat().map(([k]) => k); }
    clear() {
        this.buckets = new Array(997).fill(null).map(() => []);
        this._size = 0;
    }
}
```

---

## 성능 기준

| 지표 | 설명 |
|------|------|
| **해시 함수 품질** | 충돌 빈도 → 균등 분포일수록 O(1) 접근 |
| **로드 팩터** | size / bucket수 → 0.7 이상이면 리해싱 필요 |
| **충돌 해결 방식** | 체이닝: 메모리 증가 / 개방 주소법: 캐시 효율 |

> 실제 성능은 대량 데이터로 실측하는 것이 신뢰성 있는 평가 방법

---

## 다른 타입 키 지원 시

현재 해시 함수는 문자열 전용 (charCodeAt 사용)
→ 다른 타입 지원 시: 타입 판별 후 변환 로직 추가 필요

```javascript
_hash(key) {
    const strKey = typeof key === 'string' ? key : JSON.stringify(key);
    // 이후 동일한 해시 계산
}
```

## 관련 개념
- [[02 - Knowledge/Computing/DataStructure/컬렉션 활용\|컬렉션 활용]] - Map 활용 패턴
- [[02 - Knowledge/Computing/DataStructure/_DataStructure\|DataStructure]] - 전체 목록
