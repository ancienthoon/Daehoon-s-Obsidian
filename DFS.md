# DFS (깊이 우선 탐색, Depth-First Search)

---

## DFS란?

> **한 방향으로 갈 수 있는 끝까지 탐색한 뒤, 되돌아와 다른 방향 탐색**

```
        1
       / \
      2   3
     / \   \
    4   5   6

탐색 순서: 1 → 2 → 4 → 5 → 3 → 6
```

> 💡 **비유:** 미로에서 한 방향으로 막힐 때까지 직진 → 막히면 갈림길로 되돌아가 다른 길 선택

---

## 핵심 특징

```
자료구조: Stack / 재귀 호출 스택
탐색 순서: 깊은 곳 먼저
최단 경로: ❌ 보장 안 함
시간복잡도: O(V + E)
공간복잡도: O(V)
```

---

## 동작 원리

```
① 시작 정점 방문 + visited 체크
② 인접 정점 중 미방문 정점으로 이동
③ 더 이상 갈 곳 없으면 이전 정점으로 되돌아옴 (백트래킹)
④ 모든 정점 방문할 때까지 ②~③ 반복
```

**단계별 시각화:**

```
방문: 1 → visited[1] = true
방문: 2 → visited[2] = true
방문: 4 → visited[4] = true
  4의 인접 정점 없음 → 백트래킹
방문: 5 → visited[5] = true
  5의 인접 정점 없음 → 백트래킹 → 백트래킹
방문: 3 → visited[3] = true
방문: 6 → visited[6] = true
  종료
```

---

## 구현 방법 2가지

### 방법 1: 재귀 (일반적) ✅

```cpp
#include <iostream>
#include <vector>
using namespace std;

const int MAX = 1001;
vector<int> graph[MAX];
bool visited[MAX];

void dfs(int cur) {
    visited[cur] = true;
    cout << cur << " ";

    for (int next : graph[cur]) {
        if (!visited[next]) {
            dfs(next);          // 재귀 호출 = 깊이 탐색
        }
    }
    // 함수 종료 = 백트래킹
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

    dfs(1);
}
```

### 방법 2: 스택 (명시적)

```cpp
void dfs(int start) {
    stack<int> s;
    s.push(start);

    while (!s.empty()) {
        int cur = s.top();
        s.pop();

        if (visited[cur]) continue;
        visited[cur] = true;
        cout << cur << " ";

        for (int next : graph[cur]) {
            if (!visited[next]) {
                s.push(next);
            }
        }
    }
}
```

> 💡 재귀가 더 직관적이고 코드가 짧아서 주로 사용  
> 정점 수가 매우 많으면 스택 오버플로우 → 스택 방식 사용

---

## 재귀 vs 스택 비교

|구분|재귀|명시적 스택|
|---|---|---|
|코드 길이|짧음|길음|
|직관성|높음|보통|
|스택 오버플로우|위험 (깊이 깊을 때)|안전|
|탐색 순서|동일|역순 가능|

---

## 백트래킹 (Backtracking)

> **조건에 맞지 않는 경로는 일찍 포기하고 되돌아오는 기법**  
> DFS + 가지치기(Pruning)

```cpp
void dfs(int cur, int depth) {
    // 목표 도달
    if (depth == target) {
        print();
        return;
    }

    for (int next : graph[cur]) {
        if (!visited[next]) {
            visited[next] = true;   // 선택
            dfs(next, depth + 1);
            visited[next] = false;  // 선택 취소 (백트래킹)
        }
    }
}
```

> ⚠️ 백트래킹의 핵심: 재귀 호출 **전에** `visited = true`, 호출 **후에** `visited = false`

---

## 자주 쓰는 패턴

### 연결 요소 개수 세기

```cpp
int cnt = 0;
for (int i = 1; i <= n; i++) {
    if (!visited[i]) {
        dfs(i);
        cnt++;
    }
}
cout << cnt;
```

### 경로 존재 여부

```cpp
bool found = false;

void dfs(int cur, int target) {
    if (cur == target) {
        found = true;
        return;
    }
    visited[cur] = true;

    for (int next : graph[cur]) {
        if (!visited[next]) dfs(next, target);
    }
}
```

### 사이클 감지 (무방향 그래프)

```cpp
bool hasCycle = false;

void dfs(int cur, int parent) {
    visited[cur] = true;

    for (int next : graph[cur]) {
        if (!visited[next]) {
            dfs(next, cur);
        } else if (next != parent) {
            hasCycle = true;  // 방문했는데 부모가 아님 = 사이클
        }
    }
}
```

### 모든 경로 출력

```cpp
vector<int> path;

void dfs(int cur, int target) {
    path.push_back(cur);

    if (cur == target) {
        for (int v : path) cout << v << " ";
        cout << "\n";
    } else {
        for (int next : graph[cur]) {
            if (!visited[next]) {
                visited[next] = true;
                dfs(next, target);
                visited[next] = false;  // 백트래킹
            }
        }
    }
    path.pop_back();  // 백트래킹
}
```

### 2차원 배열 DFS (섬 넓이 구하기)

```cpp
int dx[] = {0, 0, 1, -1};
int dy[] = {1, -1, 0, 0};
int area = 0;

void dfs(int x, int y) {
    visited[x][y] = true;
    area++;

    for (int d = 0; d < 4; d++) {
        int nx = x + dx[d];
        int ny = y + dy[d];

        if (nx < 0 || nx >= n) continue;
        if (ny < 0 || ny >= m) continue;
        if (board[nx][ny] == 0) continue;
        if (visited[nx][ny]) continue;

        dfs(nx, ny);
    }
}
```

---

## DFS vs BFS 비교

|구분|DFS|BFS|
|---|---|---|
|자료구조|Stack / 재귀|Queue|
|탐색 순서|깊은 곳 먼저|가까운 곳 먼저|
|최단 경로|❌|✅|
|메모리|적음|많음|
|구현|재귀로 간단|반복문으로 간단|
|주요 용도|경로 탐색, 사이클, 백트래킹|최단 거리, 레벨 탐색|

---

## 문제 유형별 선택 기준

```
최단 거리가 필요하다    → BFS
경로의 존재 여부만 필요 → DFS
모든 경우의 수 탐색     → DFS + 백트래킹
연결 요소 / 덩어리      → DFS or BFS 둘 다 가능
사이클 감지             → DFS
순열 / 조합             → DFS + 백트래킹
```

---

## 자주 하는 실수

```cpp
// ❌ 백트래킹 시 visited 복원 누락
void dfs(int cur) {
    visited[cur] = true;
    for (int next : ...) {
        if (!visited[next]) dfs(next);
    }
    // visited[cur] = false; ← 이걸 빠뜨리면 다른 경로 탐색 불가
}

// ✅ 모든 경로 탐색 시 반드시 복원
void dfs(int cur) {
    visited[cur] = true;
    for (int next : ...) {
        if (!visited[next]) dfs(next);
    }
    visited[cur] = false;  // 백트래킹
}
```

```cpp
// ❌ 2D 배열에서 범위 체크 전에 배열 접근
if (board[nx][ny] == 1) ...  // 범위 초과 시 런타임 에러

// ✅ 범위 체크 먼저
if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
if (board[nx][ny] == 0) continue;
```

---

## 핵심 정리

```
DFS = 재귀 + visited 배열

기본 구조:
  void dfs(int cur) {
      visited[cur] = true;
      for (next : 인접) {
          if (!visited[next]) dfs(next);
      }
  }

백트래킹 구조:
  void dfs(int cur) {
      visited[cur] = true;
      for (next : 인접) {
          if (!visited[next]) dfs(next);
      }
      visited[cur] = false;  // ← 이게 백트래킹
  }

포인트:
  단순 탐색 → visited 복원 X
  모든 경로 → visited 복원 O (백트래킹)
  2D 배열   → dx[], dy[] + 범위 체크 필수
  사이클    → next != parent 조건 체크
```

---

_Tags: #Cpp #DFS #깊이우선탐색 #그래프 #백트래킹 #재귀 #알고리즘_