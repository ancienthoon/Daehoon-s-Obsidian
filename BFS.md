# BFS (너비 우선 탐색, Breadth-First Search)

---

## BFS란?

> **시작 정점에서 가까운 정점부터 차례로 탐색하는 방법**

```
레벨 1 → 레벨 2 → 레벨 3 → ...
(가까운 것부터)
```

```
        1
       / \
      2   3
     / \   \
    4   5   6

탐색 순서: 1 → 2 → 3 → 4 → 5 → 6
```

> 💡 **비유:** 돌을 물에 던졌을 때 퍼지는 물결 — 시작점에서 동심원으로 퍼져나감

---

## 핵심 특징

```
자료구조: Queue (FIFO)
탐색 순서: 가까운 것(레벨) 먼저
최단 경로: ✅ 보장 (비가중치 그래프)
시간복잡도: O(V + E)
공간복잡도: O(V)
```

---

## 동작 원리

```
① 시작 정점을 Queue에 push + visited 체크
② Queue에서 pop → 현재 정점 처리
③ 현재 정점의 인접 정점 중 미방문 정점 → push + visited 체크
④ Queue가 빌 때까지 ②~③ 반복
```

**단계별 시각화:**

```
초기:  Queue = [1],  visited = {1}

pop 1: Queue = [2, 3],   visited = {1, 2, 3}
pop 2: Queue = [3, 4, 5], visited = {1, 2, 3, 4, 5}
pop 3: Queue = [4, 5, 6], visited = {1, 2, 3, 4, 5, 6}
pop 4: Queue = [5, 6]
pop 5: Queue = [6]
pop 6: Queue = []  → 종료
```

> ⚠️ visited 체크는 **push할 때** 해야 함  
> pop할 때 체크하면 같은 정점이 Queue에 중복으로 들어갈 수 있음

---

## 기본 구현

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

const int MAX = 1001;
vector<int> graph[MAX];
bool visited[MAX];

void bfs(int start) {
    queue<int> q;

    // ① 시작 정점 처리
    q.push(start);
    visited[start] = true;

    while (!q.empty()) {
        // ② 현재 정점 꺼내기
        int cur = q.front();
        q.pop();
        cout << cur << " ";

        // ③ 인접 정점 탐색
        for (int next : graph[cur]) {
            if (!visited[next]) {
                visited[next] = true;  // push 전에 체크!
                q.push(next);
            }
        }
    }
}

int main() {
    int n, m;
    cin >> n >> m;

    for (int i = 0; i < m; i++) {
        int a, b;
        cin >> a >> b;
        graph[a].push_back(b);
        graph[b].push_back(a);  // 무방향
    }

    bfs(1);
}
```

---

## 최단 거리 구하기

> BFS의 가장 강력한 활용 — **비가중치 그래프에서 최단 거리 보장**

```cpp
int dist[MAX];

void bfs(int start) {
    queue<int> q;
    fill(dist, dist + MAX, -1);  // -1 = 미방문

    q.push(start);
    dist[start] = 0;

    while (!q.empty()) {
        int cur = q.front();
        q.pop();

        for (int next : graph[cur]) {
            if (dist[next] == -1) {         // 미방문
                dist[next] = dist[cur] + 1; // 거리 = 이전 + 1
                q.push(next);
            }
        }
    }
}

// 사용
bfs(1);
cout << dist[5];  // 1번에서 5번까지 최단 거리
```

---

## 2차원 배열 BFS (미로 탐색)

> 상하좌우로 이동하는 그리드 탐색

```cpp
#include <iostream>
#include <queue>
using namespace std;

int n, m;
int board[101][101];
int dist[101][101];
int dx[] = {0, 0, 1, -1};  // 상하좌우 x 이동
int dy[] = {1, -1, 0, 0};  // 상하좌우 y 이동

void bfs(int sx, int sy) {
    queue<pair<int,int>> q;
    fill(&dist[0][0], &dist[100][101], -1);

    q.push({sx, sy});
    dist[sx][sy] = 0;

    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();

        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d];
            int ny = y + dy[d];

            // 범위 체크 + 벽 체크 + 미방문 체크
            if (nx < 0 || nx >= n) continue;
            if (ny < 0 || ny >= m) continue;
            if (board[nx][ny] == 0) continue;  // 벽
            if (dist[nx][ny] != -1) continue;  // 방문

            dist[nx][ny] = dist[x][y] + 1;
            q.push({nx, ny});
        }
    }
}
```

**이동 방향 배열 암기:**

```cpp
int dx[] = {0, 0, 1, -1};  // 우, 좌, 하, 상
int dy[] = {1, -1, 0, 0};
```

---

## 멀티소스 BFS

> **여러 시작점에서 동시에 BFS 시작**  
> 모든 시작점을 Queue에 미리 넣고 BFS 수행

```cpp
// 예: 여러 불씨에서 동시에 번지는 화재
void bfs() {
    queue<pair<int,int>> q;

    // 모든 시작점 미리 삽입
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            if (board[i][j] == 1) {   // 불씨 위치
                q.push({i, j});
                dist[i][j] = 0;
            }

    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();

        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d];
            int ny = y + dy[d];
            if (...범위/방문 체크...) continue;
            dist[nx][ny] = dist[x][y] + 1;
            q.push({nx, ny});
        }
    }
}
```

---

## 레벨(단계)별 처리

> 같은 거리의 정점들을 묶어서 처리해야 할 때

```cpp
void bfs(int start) {
    queue<int> q;
    q.push(start);
    visited[start] = true;

    int level = 0;

    while (!q.empty()) {
        int size = q.size();   // 현재 레벨의 정점 수

        for (int i = 0; i < size; i++) {
            int cur = q.front();
            q.pop();
            cout << cur << "(레벨" << level << ") ";

            for (int next : graph[cur]) {
                if (!visited[next]) {
                    visited[next] = true;
                    q.push(next);
                }
            }
        }
        level++;
    }
}
```

---

## 자주 하는 실수

```cpp
// ❌ pop할 때 visited 체크 → 중복 삽입
while (!q.empty()) {
    int cur = q.front(); q.pop();
    if (visited[cur]) continue;  // 늦은 체크
    visited[cur] = true;
    ...
}

// ✅ push할 때 visited 체크
if (!visited[next]) {
    visited[next] = true;   // push 전에!
    q.push(next);
}
```

```cpp
// ❌ 2차원 배열에서 범위 체크 누락
int nx = x + dx[d];
int ny = y + dy[d];
if (board[nx][ny] == 1)  // 범위 초과 시 런타임 에러!

// ✅ 범위 체크 먼저
if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
if (board[nx][ny] == 0) continue;
```

---

## BFS 적용 문제 유형

|유형|설명|핵심|
|---|---|---|
|**최단 거리**|비가중치 그래프|`dist[next] = dist[cur] + 1`|
|**미로 탐색**|2차원 배열 이동|`dx[], dy[]` 방향 배열|
|**연결 요소**|덩어리 개수 세기|외부 반복문으로 미방문 정점마다 BFS|
|**멀티소스**|여러 시작점 동시 출발|시작 전 모두 Queue에 삽입|
|**레벨 탐색**|단계별 처리|`size = q.size()` 고정 후 반복|

---

## 핵심 정리

```
BFS = Queue + visited 배열

① 시작점 push + visited = true
② pop → 처리
③ 인접 정점 미방문이면 visited = true + push
④ Queue 빌 때까지 반복

포인트:
  visited는 push할 때 체크 (pop 때 X)
  최단 거리: dist[next] = dist[cur] + 1
  2D 배열: dx[], dy[] 방향 배열 + 범위 체크 필수
  멀티소스: 시작점 전부 미리 push
```

---

_Tags: #Cpp #BFS #너비우선탐색 #그래프 #최단경로 #자료구조 #알고리즘_