---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/JavaScript/ECMAScript/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

# ECMAScript (ES6+)

> JavaScript 표준 명세, ES6부터 객체지향 언어로 진화

## 개요

- ECMA에서 표준 관리
- 인터프리터 언어: 한 줄씩 컴파일되어 실행
- 2015년 ES6부터 객체지향 프로그래밍 언어로 진화

## 데이터 타입

| 분류 | 타입 | 특징 |
|------|------|------|
| **원시타입** | string, integer, boolean | 값 자체를 저장 |
| **참조타입** | 객체, 배열 | 같은 공간에서 값을 참조 → 변경 시 같은 값으로 출력 |

## ES6+ 주요 문법

### let / const
```js
let x = 1;      // 재할당 가능
const y = 2;    // 재할당 불가
```

### 화살표 함수 (Arrow Function)
```js
const add = (a, b) => a + b;
```

### 템플릿 리터럴
```js
const name = 'world';
console.log(`Hello, ${name}!`);
```

### 구조 분해 할당 (Destructuring)
```js
const [a, b] = [1, 2];
const { name, age } = { name: 'Alice', age: 25 };
```

### 스프레드 연산자
```js
const arr = [1, 2, 3];
const newArr = [...arr, 4, 5];
```

### 기본 매개변수
```js
function greet(name = 'Guest') {
  return `Hello, ${name}`;
}
```

### 클래스 (Class)
```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}
```

### Promise / async-await
```js
// Promise
fetch('/api/data')
  .then(res => res.json())
  .then(data => console.log(data));

// async-await
async function getData() {
  const res = await fetch('/api/data');
  const data = await res.json();
  return data;
}
```

### 모듈 (Module)
```js
// 내보내기
export const PI = 3.14;
export default function greet() {}

// 가져오기
import greet, { PI } from './module.js';
```

## 관련 개념
- [[02 - Knowledge/Computing/Web/JavaScript/JavaScript\|JavaScript]] - DOM, 이벤트
- [[02 - Knowledge/Computing/Web/Node.js/Node.js 기초\|Node.js]] - 서버 사이드 JS 실행 환경
- [[02 - Knowledge/Computing/Web/React/React 기초\|React]] - JSX, 컴포넌트
