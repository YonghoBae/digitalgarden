---
{"dg-publish":true,"permalink":"/02 - Knowledge/DevEnv/DB/Redis/","tags":["type/study","context/studies","theme/backend","status/completed"]}
---

- 랭체인을 이용한 챗봇을 구현을 했는데 여기서 과거 대화내역을 MYSQL에 있는 대화내역 기록을 조회해서 랭체인 메서드를 이용해서 집어넣는 형식으로 구현했다
- 그런데 이렇게하면 채팅을 칠 때마다 db 조회를 해야하므로 챗봇의 답변이 많이 느려질것이라고 생각하고 이에 대해서 Redis를 도입할려고한다.