---
{"dg-publish":true,"permalink":"/project/setting/DB/dbdiagram/"}
---


- MySQL 데이터베이스 스키마를 조회해서 dbdiagram.io에서 다이어그램으로 바로 만드는 방법
- 데이터베이스 스키마를 `.sql` 파일로 내보낸(export) 후, 그 파일을 dbdiagram.io에 가져온다(import).

### **핵심 원리**

- dbdiagram.io는 데이터베이스 구조를 정의하는 `DBML` (Database Markup Language)이라는 언어를 사용한다.
- 하지만 사용자의 편의를 위해 다른 데이터베이스의 SQL DDL(데이터 정의 언어) 파일을 직접 임포트하여 DBML로 자동 변환해주는 기능을 제공한다.


### 방법: mysqldump를 이용한 스키마 및 dbdiagram에서 가져오기

-  `mysqldump`는 MySQL 데이터베이스의 내용을 백업하는 표준 유틸리티로, 데이터 없이 구조만 내보내는 옵션을 제공합니다.

#### 1단계: MySQL에서 데이터베이스 스키마 내보내기 (SQL 파일 생성)
```
mysqldump -u [사용자이름] -p --no-data [데이터베이스이름] > schema.sql
```

- `[사용자이름]`: MySQL 접속에 사용하는 사용자 이름 (예: `root`)
- `-p`: 비밀번호를 입력하라는 프롬프트가 뜸.
- `--no-data`: 실제 데이터는 제외하고 테이블 구조, 관계 등 스키마 정보만 파일로 저장
- `[데이터베이스이름]`: 다이어그램으로 만들고 싶은 데이터베이스의 이름
- `> schema.sql`: 결과를 `schema.sql`이라는 파일로 저장

#### 2단계: dbdiagram.io에서 SQL 파일 가져오기

1. **[dbdiagram.io](https://dbdiagram.io/)** 사이트로 이동하여 새 다이어그램을 연다.
2. 상단 메뉴에서 **"Import"** 버튼을 클릭한다.
3. 드롭다운 메뉴에서 "Import from MySQL (DDL)"을 선택한다.
4. 파일 선택 창이 나타나면, 1단계에서 생성한 `schema.sql` 파일을 업로드합니다.
