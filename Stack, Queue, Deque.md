# C++ STL: Stack / Queue / Deque

---

## 세 자료구조 한눈에 비교

```
Stack  → 한쪽 끝에서만 넣고 뺌       LIFO (Last In First Out)
Queue  → 한쪽으로 넣고 반대로 뺌      FIFO (First In First Out)
Deque  → 양쪽 끝 모두 넣고 뺌         양방향
```

```
Stack:        Queue:          Deque:
  [top]       앞 ← [  ] ← 뒤   양쪽 ← [      ] → 양쪽
  [ 3 ]           [  ]
  [ 2 ]           [  ]
  [ 1 ]
```

---

## Stack

> **마지막에 넣은 것을 먼저 꺼냄 (LIFO)**  
> 💡 비유: 접시 쌓기 — 마지막에 올린 접시를 먼저 꺼냄

### 기본 사용법

```cpp
#include <stack>
using namespace std;

stack<int> s;

s.push(1);      // 삽입
s.push(2);
s.push(3);

cout << s.top();   // 3  ← 맨 위 확인 (제거 안 함)
s.pop();           // 3 제거
cout << s.top();   // 2
cout << s.size();  // 2
cout << s.empty(); // 0 (false)
```

### 주요 함수

|함수|설명|
|---|---|
|`push(x)`|맨 위에 삽입|
|`pop()`|맨 위 제거 (반환값 없음)|
|`top()`|맨 위 값 확인|
|`size()`|원소 개수|
|`empty()`|비어있으면 true|

> ⚠️ `pop()`은 값을 반환하지 않음  
> 값이 필요하면 `top()` 먼저 읽고 → `pop()` 호출

```cpp
// ✅ 올바른 패턴
while (!s.empty()) {
    cout << s.top();   // 먼저 읽고
    s.pop();           // 그 다음 제거
}
```

---

## Queue

> **먼저 넣은 것을 먼저 꺼냄 (FIFO)**  
> 💡 비유: 줄 서기 — 먼저 온 사람이 먼저 나감

### 기본 사용법

```cpp
#include <queue>
using namespace std;

queue<int> q;

q.push(1);      // 뒤에 삽입
q.push(2);
q.push(3);

cout << q.front();  // 1  ← 맨 앞 확인
cout << q.back();   // 3  ← 맨 뒤 확인
q.pop();            // 1 제거 (맨 앞)
cout << q.front();  // 2
cout << q.size();   // 2
```

### 주요 함수

|함수|설명|
|---|---|
|`push(x)`|맨 뒤에 삽입|
|`pop()`|맨 앞 제거 (반환값 없음)|
|`front()`|맨 앞 값 확인|
|`back()`|맨 뒤 값 확인|
|`size()`|원소 개수|
|`empty()`|비어있으면 true|

```cpp
// ✅ 큐 순서대로 출력
while (!q.empty()) {
    cout << q.front() << " ";
    q.pop();
}
// 입력: 1 2 3 → 출력: 1 2 3
```

---

## Deque (Double-Ended Queue)

> **앞뒤 양쪽에서 모두 삽입/삭제 가능**  
> 💡 비유: 양쪽이 열린 터널 — 어느 쪽에서든 넣고 뺄 수 있음

### 기본 사용법

```cpp
#include <deque>
using namespace std;

deque<int> dq;

dq.push_back(2);    // 뒤에 삽입  → [2]
dq.push_front(1);   // 앞에 삽입  → [1, 2]
dq.push_back(3);    // 뒤에 삽입  → [1, 2, 3]

cout << dq.front(); // 1
cout << dq.back();  // 3

dq.pop_front();     // 앞 제거  → [2, 3]
dq.pop_back();      // 뒤 제거  → [2]

// 인덱스로 접근도 가능 (vector처럼)
cout << dq[0];      // 2
```

### 주요 함수

|함수|설명|
|---|---|
|`push_front(x)`|맨 앞에 삽입|
|`push_back(x)`|맨 뒤에 삽입|
|`pop_front()`|맨 앞 제거|
|`pop_back()`|맨 뒤 제거|
|`front()`|맨 앞 값 확인|
|`back()`|맨 뒤 값 확인|
|`size()`|원소 개수|
|`empty()`|비어있으면 true|
|`dq[i]`|인덱스 접근 (vector처럼)|

---

## 세 자료구조 함수 비교표

|기능|stack|queue|deque|
|---|---|---|---|
|앞에 삽입|❌|❌|`push_front()`|
|뒤에 삽입|`push()`|`push()`|`push_back()`|
|앞 제거|❌|`pop()`|`pop_front()`|
|뒤 제거|`pop()`|❌|`pop_back()`|
|앞 확인|❌|`front()`|`front()`|
|뒤 확인|❌|`back()`|`back()`|
|현재값 확인|`top()`|-|-|
|인덱스 접근|❌|❌|`dq[i]` ✅|

---

## 자주 쓰는 패턴

### Stack — 괄호 검사

```cpp
stack<char> s;
string str = "((()))";

for (char c : str) {
    if (c == '(') s.push(c);
    else {
        if (s.empty()) { /* 짝 없음 */ break; }
        s.pop();
    }
}
bool valid = s.empty();  // 비어있으면 올바른 괄호
```

### Queue — BFS 탐색

```cpp
queue<int> q;
q.push(start);

while (!q.empty()) {
    int cur = q.front();
    q.pop();
    // 인접 노드 처리
    for (int next : graph[cur]) {
        q.push(next);
    }
}
```

### Deque — 슬라이딩 윈도우

```cpp
deque<int> dq;

for (int i = 0; i < n; i++) {
    // 범위 벗어난 앞 원소 제거
    while (!dq.empty() && dq.front() < i - k + 1)
        dq.pop_front();
    // 뒤에서 작은 값 제거
    while (!dq.empty() && arr[dq.back()] < arr[i])
        dq.pop_back();
    dq.push_back(i);
}
```

---

## 헤더 & 선언 요약

```cpp
#include <stack>    →  stack<타입> s;
#include <queue>    →  queue<타입> q;
#include <deque>    →  deque<타입> dq;
```

---

## 핵심 정리

```
Stack  LIFO  → push/pop/top         한쪽 끝
Queue  FIFO  → push/pop/front/back  한쪽 넣고 반대쪽 뺌
Deque  양방향 → push/pop_front/back  양쪽 모두 + 인덱스 접근

공통 주의:
- pop()은 반환값 없음 → 값 필요하면 top()/front() 먼저!
- empty() 확인 후 pop/top/front 호출할 것 (빈 컨테이너 접근 시 에러)
```

---

_Tags: #Cpp #STL #Stack #Queue #Deque #자료구조