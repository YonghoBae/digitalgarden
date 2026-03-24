---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/React/React 기초/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

# React 기초

> 컴포넌트 기반 UI 라이브러리, SPA, 훅

## SPA (Single Page Application)

- 최초 로드 시 HTML + 번들링된 JS 파일을 일괄 다운로드
- 이후 필요한 부분만 부분 렌더링 → 빠른 반응성, 쾌적한 UX

## JSX

JavaScript XML — JS 코드 내에서 HTML과 유사한 구문 사용

```jsx
const element = <h1>Hello, world!</h1>;
```

## 컴포넌트 (Component)

### 함수형 컴포넌트 (권장)
```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

### 클래스형 컴포넌트
```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## Props

부모 → 자식 컴포넌트로 데이터 전달

```jsx
function App() {
  return <Welcome name="Sara" />;
}
```

### PropTypes (타입 검사)
```jsx
import PropTypes from 'prop-types';

Greeting.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired
};
```

## 훅 (Hooks)

### useState — 상태 관리
```jsx
const [count, setCount] = useState(0);
```

### useEffect — 사이드 이펙트
```jsx
useEffect(() => {
  const interval = setInterval(() => {
    setSeconds(s => s + 1);
  }, 1000);

  return () => clearInterval(interval); // cleanup
}, []); // [] = 마운트 시 1회만 실행
```

> **주의**: `reactStrictMode: true`이면 개발 환경에서 useEffect가 2번 실행됨 (의도적 검사)

### useContext — 전역 상태
```jsx
const MyContext = React.createContext();

function MyProvider({ children }) {
  const [value, setValue] = useState('Hello!');
  return (
    <MyContext.Provider value={{ value, setValue }}>
      {children}
    </MyContext.Provider>
  );
}

function MyComponent() {
  const { value } = React.useContext(MyContext);
  return <div>{value}</div>;
}
```

### Custom Hooks — 로직 재사용
```jsx
function useFetch(url) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => setData(data));
  }, [url]);

  return data;
}
```

## Next.js

```
v13~: App Router (기본값)
v12 이하: Page Router
```

### Tailwind CSS 주요 패키지
```bash
npm i @headlessui/react@latest   # UI 컴포넌트
npm i @heroicons/react           # 아이콘
npm i @tailwindcss/forms         # 폼 스타일링
```

### SEO
- HTML `<head>` 영역 내용에 따라 검색엔진 노출 여부 결정
- 서버에서 요청 시 해당 서버도 클라이언트 역할 수행

## 관련 개념
- [[02 - Knowledge/Computing/Web/JavaScript/ECMAScript\|ECMAScript]] - ES6+ 문법
- [[02 - Knowledge/Computing/Web/Node.js/Node.js 기초\|Node.js]] - 개발 환경, 번들러
