---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Web/Node.js/Node.js 기초/","tags":["type/study","context/studies","theme/web","status/completed"]}
---

# Node.js 기초

> 크롬 V8 엔진 기반 JS 런타임, 이벤트 기반 논블로킹 I/O

## 개요

- **런타임**: 특정 언어로 만든 프로그램을 실행할 수 있는 환경
- 이벤트 기반 개발 모델
- **논블로킹 I/O**: 여러 작업을 병렬적으로 처리 (비동기)
- NPM을 통한 오픈소스 생태계

## 동기 vs 비동기

| 구분 | 설명 |
|------|------|
| **동기(Sync)** | 작업을 직렬로 순차 처리 |
| **비동기(Async)** | 작업을 병렬로 동시에 처리 가능 |

## NPM (Node Package Manager)

세계 최대의 재사용 가능한 오픈소스 저장소

```bash
npm init                          # package.json 생성
npm install 패키지명              # 패키지 설치
npm i 패키지명 --save-dev         # 개발환경 전용 설치
npm i -g 패키지명                 # 전역 설치
npm uninstall 패키지명            # 패키지 제거
```

## package.json

- 패키지 정보, 버전, 설치된 패키지 이력 관리
- **dependencies**: 프로덕션에서 사용하는 패키지
- **devDependencies**: 개발 시에만 사용하는 패키지

## 관련 파일

| 파일/폴더 | 역할 |
|-----------|------|
| `node_modules/` | 설치된 패키지 실제 파일 위치 |
| `package-lock.json` | 패키지 간 의존 관계 정보 |

> `package.json`이 있으면 `npm install`로 전체 패키지 자동 복원

## NVM (Node Version Manager)

```bash
nvm install 버전명      # 특정 버전 설치
nvm use 버전명          # 특정 버전 활성화 (★ 표시)
nvm uninstall 버전명    # 특정 버전 삭제
```

## nodemon

- 소스 변경 저장 시 서버 자동 재실행
- 개발 생산성 극대화
- **주의**: WSL에서 Windows 디렉토리 사용 시 변경 감지 문제 → home 디렉토리로 이동

## 템플릿 엔진

| 엔진 | 특징 |
|------|------|
| **Pug(Jade)** | 별도 문법 학습 필요, 간결 |
| **EJS** | HTML 기반 동적 생성 |

```js
// EJS v2.x
<% include ./header.ejs %>

// EJS v3.x
<%- include('./header.ejs', { data: "aaa" }) %>
```

## CORS

- 클라이언트 서버와 API 서버의 도메인이 다를 때 발생하는 보안 이슈
- 크로스 도메인 요청 차단

## 세션 관리 (분산 서버)

- 한 서버의 메모리에 저장된 세션 → 다른 서버에서 인식 불가
- 해결: **RDBMS에 세션 저장** (DB는 공유되므로)

## 데이터 분류

| 종류 | 설명 | 저장소 |
|------|------|--------|
| **정형데이터** | 형태가 정해진 데이터 | 관계형 DB |
| **비정형데이터** | 형태가 정해지지 않은 데이터 | NoSQL |

## 관련 개념
- [[02 - Knowledge/Computing/Web/JavaScript/ECMAScript\|ECMAScript]] - async/await, Promise
- [[02 - Knowledge/Computing/Web/REST API\|REST API]] - API 서버 구현
- [[02 - Knowledge/Computing/CS/Database/_Database\|Database]] - Sequelize ORM
