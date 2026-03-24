---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/JavaScript/JavaScript/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

## JavaScript
- 동적 타이핑, 객체 지향, 함수 프로그래밍 등 활용도 높은 스크립트 언어
- 높은 수준의 추상화로 개발자가 세부 사항을 잘 몰라도 프로그래밍 가능

### 객체지향적 특징
- abstraction: 객체나 함수를 통한 추상화 제공
- inheritance & polymorphism: class기반 상속이 아닌 prototype 개념 활용
- encapsulation: closure 활용
### 스크립트 언어
- 일반적으로 인터프리터 방식으로 동작
	- 최근의 인터프리터들은 대부분 부분적으로 Just-In-Time 컴파일러를 사용
- 호스트 실행 환경에 종속적
- 일반적으로 독립적인 실행파일을 만들지 않음
### 다양한 애플리케이션 제작 가능
- 웹 클라이언트 뿐 아니라 node.js를 이용해 backend 부분도 제작
	- vscode는 javascript(typescript) 기반으로 게작됨


## Html에서 JavaScript 사용 선언
### Html 파일 내에 \<script\> 태그를 이용한 영역 표시
- 일반적으로 DOM 로딩이 완료된 후 사용하기 위해 \<body\>태그 아래에 위치
- 페이지 내에서만 재사용 가능
```html
<!DOCTYPE html>
<html>
<head>
	<title>JavaScript</title>
	<script>
		console.log("Hello JavaScript!! - title");
	</script>
</head>

<body>
	<h1>Hello</h1>
</body>

<script>
	console.log("Hello JavaScript!! - body");
</script>

</html>
```