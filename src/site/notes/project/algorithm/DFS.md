---
{"dg-publish":true,"permalink":"/project/algorithm/DFS/"}
---


## DFS 특징
- 유일하게 결정X 
	- why? 순서가 없기 때문에
## DFS 구현 방법
### recursive(visit flag)

#### 인접행렬
```
void DFS(int v)
{
    visited[v] = 1;   //시작점 방문기록
    cout << v << " "; //방문한 노드 출력

    for (int i = 0; i < N; i++)
    {
        if (arr[v][i] == 1 && visited[i] == 0) // edge가 있고 방문하지 않은 노드일 경우
        {
            DFS(i);
        }
        if (i == N) // 방문하지 않은 노드가 없을 경우
            return;
    }
}
```
#### node
```
void dfs(int v)
{
	nodePointer w;
	visited[v]  = true;
	printf("%d",v);
	for(w = graph[v]; w; w=w->link) 
		if(!visited[w->vertex]) dfs(w->vertex);
}
```

### iterative(stack + visit flag)
#### 의사코드
```
1. 시작 정점(= v) 결정
2. stack의 top(= w)읽기
3. 방문하지 않은 w가 존재 => push(w) & visite 표시
4. 방문하지 않은 w가 없음 => pop()
5. 스택이 공백이 될 때 까지 2~4번 반복 수행
```

#### iterative
```
#include <iostream>

#include <vector>

#include <stack>

#include <algorithm>

using namespace std;

  

int main()

{

    int u[25][25] = {0};

    int x[4] = {0, 0, 1, -1};

    int y[4] = {1, -1, 0, 0};

    bool v[25][25] = {false}; // 방문 여부

    int n;

    cin >> n; // n 값 입력

  

    // 행렬 입력

    for (int i = 0; i < n; ++i)

    {

        for (int j = 0; j < n; ++j)

        {

            cin >> u[i][j];

            v[i][j] = false;

        }

    }

  

    stack<pair<int, int>> s;

    pair<int, int> a;

  

    // 탐색

    for (int i = 0; i < n; ++i)

    {

        for (int j = 0; j < n; ++j)

        {

            if (u[i][j] == 1 && !v[i][j]) // 시작 노드

            {

                s.push({i, j});

            }

  

            while (!s.empty())

            {

                a = s.top();

                s.pop();

  

                if (u[a.first][a.second] == 1 && !v[a.first][a.second])

                {

                    v[a.first][a.second] = true;

  

                    // 4방향 탐색

                    for (int k = 0; k < 4; ++k)

                    {

                        int nx = a.first + x[k];

                        int ny = a.second + y[k];

  

                        // 경계 확인 및 조건 확인

                        if (nx >= 0 && nx < n && ny >= 0 && ny < n &&

                            u[nx][ny] == 1 && !v[nx][ny])

                        {

                            s.push({nx, ny});

                        }

                    }

                }

            }

        }

    }

  

    return 0;

}
```