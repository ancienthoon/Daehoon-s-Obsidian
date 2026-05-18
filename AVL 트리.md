# 📘 자료구조 — AVL 트리 (AVL Tree)

---

## 🔖 목차

1. [[#개념 및 배경]]
2. [[#균형 인수 Balance Factor]]
3. [[#4가지 불균형 케이스와 회전]]
4. [[#LL 회전 우회전]]
5. [[#RR 회전 좌회전]]
6. [[#LR 회전]]
7. [[#RL 회전]]
8. [[#삽입 예제]]
9. [[#코드 구현 C++]]
10. [[#시간 복잡도]]
11. [[#핵심 정리]]

---

## 📌 개념 및 배경

> **AVL 트리**: BST의 편향 문제를 해결하기 위해 **자동으로 균형을 유지**하는 이진 탐색 트리

- 1962년 Adelson-Velsky & Landis 고안 (이름 앞글자에서 AVL)
- 삽입/삭제 후 균형이 깨지면 **회전(Rotation)** 으로 즉시 재조정
- 모든 연산을 항상 **O(log n)** 에 보장

### BST vs AVL 비교

|항목|BST|AVL|
|---|---|---|
|탐색 (평균)|O(log n)|O(log n)|
|탐색 (최악)|O(n)|**O(log n) 보장**|
|균형 유지|❌|✅ 자동 회전|
|구현 복잡도|단순|복잡|

---

## 📌 균형 인수 Balance Factor

> **Balance Factor (BF)** = 왼쪽 서브트리 높이 − 오른쪽 서브트리 높이

```
BF = height(left) - height(right)
```

### AVL 조건

```
모든 노드의 BF ∈ {-1, 0, +1}
```

|BF 값|상태|
|---|---|
|+1|왼쪽이 1 더 높음 (허용)|
|0|완전 균형 (허용)|
|-1|오른쪽이 1 더 높음 (허용)|
|+2 이상|⚠️ 왼쪽 과다 — 회전 필요|
|-2 이하|⚠️ 오른쪽 과다 — 회전 필요|

### BF 계산 예시

```
       8  (BF = 3-2 = +1)
      / \
     3   10  (BF = 1-1 = 0)
    / \    \
   1   6    14
       |
       4
```

---

## 📌 4가지 불균형 케이스와 회전

> 불균형이 발생하는 위치에 따라 4가지로 분류

```
BF = +2 (왼쪽 과다)
  ├── LL: 왼쪽 자식의 왼쪽에서 삽입 → 우회전 1회
  └── LR: 왼쪽 자식의 오른쪽에서 삽입 → 좌회전 후 우회전

BF = -2 (오른쪽 과다)
  ├── RR: 오른쪽 자식의 오른쪽에서 삽입 → 좌회전 1회
  └── RL: 오른쪽 자식의 왼쪽에서 삽입 → 우회전 후 좌회전
```

---

## 📌 LL 회전 (우회전)

> 불균형 노드의 **왼쪽-왼쪽** 방향에서 삽입 → **우회전 1회**

```
삽입 전:           3 삽입 후:        우회전 후:
    5 (BF=+2)          5 (BF=+3?)        4
   /                  /                 / \
  4 (BF=+1)          4 (BF=+2)         3   5
                     /
                    3 (BF=0)
```

### 회전 메커니즘

```
     A (BF=+2)          B
    /           →      / \
   B                  C   A
  /
 C
```

```
① B가 새 루트가 됨
② A는 B의 오른쪽 자식이 됨
③ B의 기존 오른쪽 자식은 A의 왼쪽 자식으로 이동
```

---

## 📌 RR 회전 (좌회전)

> 불균형 노드의 **오른쪽-오른쪽** 방향에서 삽입 → **좌회전 1회**

```
     A (BF=-2)          B
      \          →     / \
       B               A   C
        \
         C
```

```
① B가 새 루트가 됨
② A는 B의 왼쪽 자식이 됨
③ B의 기존 왼쪽 자식은 A의 오른쪽 자식으로 이동
```

---

## 📌 LR 회전

> 불균형 노드의 **왼쪽-오른쪽** 방향에서 삽입 → **좌회전 후 우회전**

```
     A (BF=+2)        A          C
    /          →     /   →      / \
   B                C           B   A
    \              /
     C            B
```

### 2단계 과정

```
Step 1: B를 기준으로 좌회전 (B-C 부분)
Step 2: A를 기준으로 우회전 (전체)
```

---

## 📌 RL 회전

> 불균형 노드의 **오른쪽-왼쪽** 방향에서 삽입 → **우회전 후 좌회전**

```
     A (BF=-2)          A              C
      \          →       \     →      / \
       B                  C           A   B
      /                    \
     C                      B
```

### 2단계 과정

```
Step 1: B를 기준으로 우회전 (B-C 부분)
Step 2: A를 기준으로 좌회전 (전체)
```

---

## 📌 4가지 회전 요약표

|케이스|BF|불균형 위치|해결 방법|회전 횟수|
|---|---|---|---|---|
|**LL**|+2|왼쪽의 왼쪽|우회전|1회|
|**RR**|-2|오른쪽의 오른쪽|좌회전|1회|
|**LR**|+2|왼쪽의 오른쪽|좌회전 → 우회전|2회|
|**RL**|-2|오른쪽의 왼쪽|우회전 → 좌회전|2회|

> **암기법**: L이 2개(LL/LR) → 위에서 BF=+2 / R이 2개(RR/RL) → BF=-2

---

## 📌 삽입 예제

### 1, 2, 3, 4, 5 순서로 삽입

**Step 1 — 1 삽입**

```
1
```

**Step 2 — 2 삽입**

```
1
 \
  2
```

**Step 3 — 3 삽입 → RR 불균형**

```
1 (BF=-2)     좌회전      2
 \            →           / \
  2 (BF=-1)             1   3
   \
    3
```

**Step 4 — 4 삽입**

```
  2
 / \
1   3
     \
      4
```

**Step 5 — 5 삽입 → 3에서 RR 불균형**

```
  2               2
 / \     →       / \
1   3 (BF=-2)   1   4
     \             / \
      4            3   5
       \
        5
```

---

## 📌 코드 구현 C++

```cpp
#include <iostream>
using namespace std;

struct Node {
    int data, height;
    Node *left, *right;
    Node(int v) : data(v), height(1), left(nullptr), right(nullptr) {}
};

int height(Node* n) { return n ? n->height : 0; }

int bf(Node* n) { return n ? height(n->left) - height(n->right) : 0; }

void updateHeight(Node* n) {
    if (n) n->height = 1 + max(height(n->left), height(n->right));
}

// 우회전 (LL 케이스)
Node* rotateRight(Node* A) {
    Node* B = A->left;
    Node* T = B->right;    // B의 오른쪽 서브트리
    B->right = A;
    A->left  = T;
    updateHeight(A);
    updateHeight(B);
    return B;              // B가 새 루트
}

// 좌회전 (RR 케이스)
Node* rotateLeft(Node* A) {
    Node* B = A->right;
    Node* T = B->left;     // B의 왼쪽 서브트리
    B->left  = A;
    A->right = T;
    updateHeight(A);
    updateHeight(B);
    return B;              // B가 새 루트
}

// 삽입 후 균형 조정
Node* balance(Node* n) {
    updateHeight(n);
    int b = bf(n);

    if (b > 1) {                          // 왼쪽 과다
        if (bf(n->left) < 0)              // LR 케이스
            n->left = rotateLeft(n->left);
        return rotateRight(n);            // LL 또는 LR 2단계
    }
    if (b < -1) {                         // 오른쪽 과다
        if (bf(n->right) > 0)             // RL 케이스
            n->right = rotateRight(n->right);
        return rotateLeft(n);             // RR 또는 RL 2단계
    }
    return n;                             // 이미 균형
}

Node* insert(Node* root, int val) {
    if (!root) return new Node(val);
    if (val < root->data)
        root->left  = insert(root->left,  val);
    else if (val > root->data)
        root->right = insert(root->right, val);
    return balance(root);
}

// 중위 순회
void inorder(Node* root) {
    if (!root) return;
    inorder(root->left);
    cout << root->data << "(h=" << root->height << ") ";
    inorder(root->right);
}

int main() {
    Node* root = nullptr;
    for (int v : {1, 2, 3, 4, 5})
        root = insert(root, v);
    inorder(root);
    // 출력: 1 2 3 4 5 (정렬 유지 확인)
    return 0;
}
```

---

## 📌 시간 복잡도

|연산|BST 최악|AVL|
|---|---|---|
|탐색|O(n)|**O(log n)**|
|삽입|O(n)|**O(log n)**|
|삭제|O(n)|**O(log n)**|
|공간|O(n)|O(n) + 높이 저장|

> AVL은 항상 높이가 O(log n) 이하로 유지됨이 수학적으로 증명됨

---

## 🗂️ 핵심 정리

```
AVL = BST + 균형 인수(BF) 유지
  BF = height(left) - height(right)
  허용 범위: -1 ≤ BF ≤ +1

불균형 발생 시 회전:
  BF = +2 → LL(우회전) 또는 LR(좌→우회전)
  BF = -2 → RR(좌회전) 또는 RL(우→좌회전)
```

### ⚠️ 최다 실수 포인트

|실수|올바른 내용|
|---|---|
|LR/RL 회전 순서 헷갈림|LR = 좌회전 **먼저** → 우회전 / RL = 우회전 먼저 → 좌회전|
|회전 후 높이 갱신 안 함|`updateHeight()` 자식 → 부모 순으로 반드시 갱신|
|BF 공식 반대로 계산|BF = **왼쪽** - 오른쪽 (반대가 아님)|

---

> 📅 작성일: 2026-05-18 🏫 과목: 자료구조 📖 단원: 트리 — AVL 트리

#자료구조 #트리 #AVL #균형트리 #회전 #avl_tree