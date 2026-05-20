# 📘 그래프 — A* 알고리즘 (A-Star Algorithm)

---

## 🔖 목차

1. [[#개념 및 배경]]
2. [[#핵심 공식 f = g + h]]
3. [[#휴리스틱 함수 h]]
4. [[#동작 과정 단계별]]
5. [[#예제 격자 맵]]
6. [[#코드 구현 C++]]
7. [[#다익스트라 vs A* 비교]]
8. [[#시간 복잡도]]
9. [[#핵심 정리]]

---

## 📌 개념 및 배경

> __A_ 알고리즘_*: 시작점에서 목표점까지의 최단 경로를 **효율적으로** 찾는 알고리즘 다익스트라에 **목표 방향 정보(휴리스틱)**를 더한 형태

- 1968년 피터 하트, 닐스 닐슨, 버트럼 래펠 공동 개발
- **Best-First Search + 다익스트라** 의 결합
- 게임 AI 경로 탐색, 지도 내비게이션에서 가장 널리 사용

### 다익스트라와의 차이

```
다익스트라: 시작점에서 모든 방향으로 균등하게 탐색 (목표 방향 모름)
A*         : 목표까지의 예상 거리(h)를 이용해 목표 방향으로 우선 탐색
```

---

## 📌 핵심 공식 f = g + h

$$f(n) = g(n) + h(n)$$

|기호|이름|의미|
|---|---|---|
|`f(n)`|총 예상 비용|이 정점을 통과할 때의 전체 예상 경로 비용|
|`g(n)`|실제 비용|시작점 → 현재 정점 n 까지의 **실제** 이동 비용|
|`h(n)`|휴리스틱|현재 정점 n → 목표점까지의 **예상** 비용|

### 직관적 이해

```
f = 지금까지 온 실제 거리 + 앞으로 가야 할 예상 거리
    ↑ 확정된 값               ↑ 추정값 (휴리스틱)

f 가 작은 정점을 우선 탐색
→ 목표에 가까우면서 지금까지 비용도 작은 경로 우선
```

---

## 📌 휴리스틱 함수 h

> **휴리스틱(Heuristic)**: 정확한 값은 아니지만, 목표까지의 거리를 **추정**하는 함수

### 허용 가능한 휴리스틱 (Admissible Heuristic)

> A*가 **최적 해(최단 경로)**를 보장하려면 h가 실제 비용을 **절대 과대평가하지 않아야** 함

$$h(n) \leq \text{실제 비용}(n \to \text{목표})$$

### 자주 쓰이는 휴리스틱

|이름|공식|사용 조건|
|---|---|---|
|**맨해튼 거리**|`|x1-x2|
|**유클리드 거리**|`sqrt((x1-x2)² + (y1-y2)²)`|자유 이동 가능 환경|
|**체비쇼프 거리**|`max(|x1-x2|
|**h = 0**|항상 0|→ 다익스트라와 동일하게 동작|

### 휴리스틱 품질에 따른 성능

```
h = 0         → 다익스트라와 동일 (느림, 모든 방향 탐색)
h ≤ 실제 비용  → 최적 해 보장, 속도 향상
h = 실제 비용  → 완벽한 휴리스틱, 최고 속도
h > 실제 비용  → 최적 해 미보장, 빠르지만 부정확 가능
```

---

## 📌 동작 과정 단계별

### 사용하는 두 리스트

|리스트|설명|
|---|---|
|**Open List**|탐색 후보 정점들 (f값 기준 정렬된 우선순위 큐)|
|**Closed List**|이미 처리 완료된 정점들|

### 알고리즘 흐름

```
① 시작 정점을 Open에 추가, g=0, h=h(시작→목표), f=g+h
② Open이 비어있으면 → 경로 없음 종료
③ Open에서 f 값이 가장 작은 정점 u 선택
④ u가 목표 정점이면 → 경로 역추적 후 종료 ✅
⑤ u를 Open에서 제거, Closed에 추가
⑥ u의 각 이웃 v에 대해:
   a. v가 Closed에 있으면 스킵
   b. tentative_g = g[u] + w(u,v) 계산
   c. v가 Open에 없거나 tentative_g < g[v] 이면:
      - g[v] = tentative_g
      - f[v] = g[v] + h[v]
      - prev[v] = u
      - v를 Open에 추가 (또는 업데이트)
⑦ ②로 돌아가 반복
```

---

## 📌 예제 격자 맵

### 맵 설정

```
S = 시작(0,0),  G = 목표(4,4),  # = 장애물
이동 비용: 상하좌우 = 1,  대각선 = 1.4 (√2 근사)
휴리스틱: 맨해튼 거리

  0   1   2   3   4
0 [S] [ ] [ ] [ ] [ ]
1 [ ] [#] [#] [ ] [ ]
2 [ ] [#] [ ] [ ] [ ]
3 [ ] [ ] [ ] [#] [ ]
4 [ ] [ ] [ ] [ ] [G]
```

### 단계별 진행 (4방향 이동, 단순화)

**초기 상태**

```
Open:   {(0,0), g=0, h=8, f=8}
Closed: {}
```

**Step 1 — (0,0) 처리**

```
이웃: (1,0), (0,1)
(1,0): g=1, h=7, f=8  → Open 추가
(0,1): g=1, h=7, f=8  → Open 추가
Closed: {(0,0)}
```

**Step 2 — f=8인 정점 선택 (예: (1,0))**

```
이웃: (2,0), (1,1)=장애물 스킵
(2,0): g=2, h=6, f=8  → Open 추가
Closed: {(0,0), (1,0)}
```

**…계속 진행하면 결국 목표 (4,4)에 도달**

### 다익스트라와 탐색 범위 비교

```
다익스트라       A*
□□□□□□□□    □□□□□□□□
□□□□□□□□    □□□□□□□□
□□□□□□□□    □□□□□□□□
□S□□□□□□    □S□□□□□□
□□□□□□□□    □□□□□□□□
□□□□□□□□    □□□□□□□□
□□□□□□□□    □□□□□□□□
□□□□□□□G    □□□□□□□G

← 원형으로 퍼짐 →    ← 목표 방향으로 집중 탐색 →
(더 많은 정점 탐색)   (더 적은 정점 탐색, 빠름)
```

---

## 📌 코드 구현 C++

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <cmath>
#include <climits>
using namespace std;

struct Node {
    int x, y;
    double f, g, h;
    bool operator>(const Node& o) const { return f > o.f; }
};

// 맨해튼 휴리스틱
double heuristic(int x, int y, int gx, int gy) {
    return abs(x - gx) + abs(y - gy);
}

// 격자 맵 A*
vector<pair<int,int>> astar(vector<vector<int>>& grid,
                             pair<int,int> start,
                             pair<int,int> goal) {
    int rows = grid.size(), cols = grid[0].size();
    int sx = start.first, sy = start.second;
    int gx = goal.first,  gy = goal.second;

    // dx, dy: 4방향 이동
    int dx[] = {0, 0, 1, -1};
    int dy[] = {1, -1, 0, 0};

    vector<vector<double>> g(rows, vector<double>(cols, 1e9));
    vector<vector<pair<int,int>>> prev(rows, vector<pair<int,int>>(cols, {-1,-1}));
    vector<vector<bool>> closed(rows, vector<bool>(cols, false));

    priority_queue<Node, vector<Node>, greater<Node>> open;

    g[sx][sy] = 0;
    double h0 = heuristic(sx, sy, gx, gy);
    open.push({sx, sy, h0, 0, h0});

    while (!open.empty()) {
        Node cur = open.top(); open.pop();
        int cx = cur.x, cy = cur.y;

        if (closed[cx][cy]) continue;
        closed[cx][cy] = true;

        // 목표 도달
        if (cx == gx && cy == gy) {
            // 경로 역추적
            vector<pair<int,int>> path;
            for (auto p = make_pair(gx, gy); p != make_pair(-1,-1); p = prev[p.first][p.second])
                path.push_back(p);
            reverse(path.begin(), path.end());
            return path;
        }

        for (int d = 0; d < 4; d++) {
            int nx = cx + dx[d], ny = cy + dy[d];
            if (nx < 0 || nx >= rows || ny < 0 || ny >= cols) continue;
            if (grid[nx][ny] == 1 || closed[nx][ny]) continue;  // 1 = 장애물

            double ng = g[cx][cy] + 1;  // 이동 비용 = 1
            if (ng < g[nx][ny]) {
                g[nx][ny] = ng;
                double nh = heuristic(nx, ny, gx, gy);
                prev[nx][ny] = {cx, cy};
                open.push({nx, ny, ng + nh, ng, nh});
            }
        }
    }
    return {};  // 경로 없음
}

int main() {
    vector<vector<int>> grid = {
        {0, 0, 0, 0, 0},
        {0, 1, 1, 0, 0},
        {0, 1, 0, 0, 0},
        {0, 0, 0, 1, 0},
        {0, 0, 0, 0, 0}
    };

    auto path = astar(grid, {0,0}, {4,4});

    cout << "경로: ";
    for (auto [x, y] : path)
        cout << "(" << x << "," << y << ") ";
    cout << endl;
    return 0;
}
```

---

## 📌 다익스트라 vs A* 비교

|항목|다익스트라|A*|
|---|---|---|
|탐색 기준|`g(n)` 만 사용|`f(n) = g(n) + h(n)`|
|목표 방향성|❌ 없음 (모든 방향 균등)|✅ 있음 (목표 방향 우선)|
|최적 해 보장|✅ 항상|✅ h가 허용 가능할 때|
|탐색 노드 수|많음|적음 (휴리스틱 품질에 따라)|
|속도|상대적으로 느림|상대적으로 빠름|
|음수 가중치|❌ 불가|❌ 불가|
|단일 목표 탐색|비효율적|✅ 최적화됨|
|모든 정점 최단 거리|✅ 구할 수 있음|❌ 목표 하나에 집중|

### 언제 어떤 걸 쓸까?

```
목표 정점이 하나, 격자나 게임 맵
  → A* (빠르고 효율적)

모든 정점까지의 최단 거리 필요
  → 다익스트라

최단 거리가 중요하고 휴리스틱 설계 어려움
  → 다익스트라
```

---

## 📌 시간 복잡도

|조건|시간 복잡도|
|---|---|
|일반적|O((V + E) log V)|
|최악 (h=0, 다익스트라와 동일)|O((V + E) log V)|
|완벽한 휴리스틱|O(경로 길이)|

> 실제로는 휴리스틱 품질에 따라 성능이 크게 달라짐

---

## 🗂️ 핵심 정리

### A* 핵심 3줄 요약

```
1. f(n) = g(n) + h(n) — 이게 전부
2. Open에서 f 최솟값 선택 → 탐색
3. h는 실제 값을 넘으면 안 됨 (허용 가능 조건)
```

### 휴리스틱 선택 요약

|이동 방식|추천 휴리스틱|
|---|---|
|4방향 격자|맨해튼 거리|
|8방향 격자|체비쇼프 거리|
|자유 이동|유클리드 거리|

### ⚠️ 최다 실수 포인트

|실수|올바른 처리|
|---|---|
|h가 실제 비용 초과|최적 해 미보장 → 허용 가능 휴리스틱 사용|
|Closed 체크 안 함|이미 처리된 정점 재처리 → 무한 루프 가능|
|h=0으로 설정|다익스트라와 동일 동작, A* 효율 없음|
|방향 그래프에서 역방향 간선 포함|그래프 방향 확인 필수|

### 관련 알고리즘 링크

- 다익스트라 — A*의 기반, h=0이면 동일
- BFS — 비가중치 그래프 최단 경로
- 벨만-포드 — 음수 가중치 처리

---

> 📅 작성일: 2026-05-18 🏫 과목: 자료구조 / 알고리즘 📖 단원: 그래프 — 최단 경로 탐색

#알고리즘 #그래프 #A스타 #최단경로 #휴리스틱 #astar