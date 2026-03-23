---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/SSR vs CSR/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

# SSR vs CSR

> 웹 페이지를 어디서 렌더링하느냐에 따른 두 가지 방식

## SSR (Server Side Rendering, 서버 사이드 렌더링)

- 서버에서 **완성된 HTML**을 생성해 클라이언트에 전달
- 브라우저는 받은 HTML을 바로 표시 → **초기 로딩 빠름**
- 검색 엔진이 완성된 HTML을 크롤링 → **SEO 유리**
- 페이지 이동 시마다 서버 요청 발생 → 서버 부하 증가
- 대표: **Next.js**

## CSR (Client Side Rendering, 클라이언트 사이드 렌더링)

- 서버는 빈 HTML + JavaScript를 전달, **브라우저에서 렌더링**
- 초기 JS 번들 로드 후 렌더링 → **초기 로딩 느림**
- 페이지 이동 시 서버 재요청 없이 JS로 처리 → **인터랙션 빠름**
- 검색 엔진이 JS 실행 전 빈 HTML을 보게 됨 → **SEO 불리**
- 대표: **React** (SPA)

## 비교

| 항목 | SSR | CSR |
|------|-----|-----|
| **렌더링 위치** | 서버 | 브라우저 |
| **초기 로딩** | 빠름 | 느림 (JS 번들 로드) |
| **인터랙션** | 상대적으로 느림 | 빠름 |
| **SEO** | 유리 | 불리 |
| **서버 부하** | 높음 | 낮음 |
| **대표 프레임워크** | Next.js | React (SPA) |

## 면접 포인트

- **SSR이 유리한 경우**: 콘텐츠 중심 사이트(블로그, 뉴스), SEO 중요 서비스
- **CSR이 유리한 경우**: 인터랙션 중심 앱(대시보드, 웹앱), 로그인 후 사용
- **SSR의 TTFB**: 서버 처리 시간이 길면 오히려 느릴 수 있음
- **하이드레이션(Hydration)**: SSR로 HTML 전달 후 CSR처럼 JS가 이벤트를 붙이는 과정

## 관련 개념

- [[02 - Knowledge/Computing/Web/React/_React\|React]] — CSR 대표 라이브러리
- [[02 - Knowledge/Computing/Web/JavaScript/웹 프로그램\|웹 프로그램]] — 브라우저 렌더링 과정
