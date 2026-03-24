---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/JavaScript/DOM/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

## 이벤트 처리 기본
### event와 event listener


### this
- 현재 실행 문맥(context) - 현재 함수를 호출한 객체
	- 자바와 달리 함수가 선언될 때가 아니라 함수가 호출될 때 동적으로 결정

### 다양한 this binding
#### 기본 바인딩
- 일반적인 방식으로 그 함수를 호출한 객체
- non-strict에서 호출 객체 생략 시 window

#### 묵시적 바인딩

#### 명시적 바인딩

#### new 바인딩





### arrow 함수와 this
- arrow함수는 자신만의 this를 가지지 않음
	- 자신이 선언된 상위 scope의 this를 그대로 물려받음(lexical this)


### event listener 등록 방식
- inline 스타일: callback을 등록하지 않고 html 태그의 속성으로 handler를 직접 실행하는 형태
	- html과 javascript가 섞임 - 관심사 분리가 되지 않아 유지보수가 어렵고 재사용성이 떨어짐
		- Handler 내부에는 'event' 라는 내장 변수가 생성되며 'this'는 window 객체로 고정됨

