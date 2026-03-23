---
{"dg-publish":true,"permalink":"/02 - Knowledge/Computing/Algorithm/Graph/KRUSKAL/","tags":["type/study","context/studies","theme/algorithm","status/completed"]}
---

## 간선을 하나씩 선택해서 [[02 - Knowledge/Computing/Algorithm/Graph/MST\|MST]]를 찾는 알고리즘
1. 최초, 모든 간선을 가중치에 따라 오름차순으로 정렬
2. 가중치가 가장 낮은 간선부터 선택하면서 트리를 증가시킴
	- 사이클이 존재하면 남아 있는 간선 중 그 다음으로 가중치가 낮은 간선 선택
3. n-1 개의 간선이 선택될 때까지 2를 반복


