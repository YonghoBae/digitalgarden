---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Algorithm/Graph/BFS/","tags":["type/study","context/studies","theme/algorithm","status/completed"]}
---

- 너비우선탐색은 탐색 시작점의 인접한 정점을 먼저 모두 차례로 방문한 후에 방문했던 정점을 시작점으로 하여 다시 인접한 정점들을 차례로 방문하는 방식
- 인접한 정점들에 대해 탐색을 한 후, 차례로 다시 너비우선탐색을 진행해야 하므로, 선입선출 형태의 자료구조인 **큐**를 활용함.

```pascal
BFS(V)
	큐 생성
	방문관리 배열 생성
	시작 정점 V를 큐에 삽입
	정점 V를 방문한 것으로 표시
	while (큐가 비어 있지 않은 경우) {
		t <- 큐의 첫 번째 원소 반환
		for (t와 연결된 모든 간선에 대해) {
			u <- t의 인접 정점
			u가 방문되지 않은 곳이면,
			u를 큐에 넣고, 방문한 것으로 표시
		}
	}
```