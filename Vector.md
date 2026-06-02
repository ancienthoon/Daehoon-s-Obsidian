# 📘 C++ vector 클래스

#cpp #vector #STL #컨테이너

---

## 📋 목차

- [[#🧩 vector란?]]
- [[#🔧 선언 및 초기화]]
- [[#📥 원소 추가 및 삽입]]
- [[#📤 원소 삭제]]
- [[#🔍 원소 접근]]
- [[#📏 크기와 용량]]
- [[#🔁 반복자 (Iterator)]]
- [[#🔄 정렬 및 탐색]]
- [[#🧩 2차원 vector]]
- [[#⚠️ 핵심 정리 & 자주 하는 실수]]

---

## 🧩 vector란?

- C++ STL에서 제공하는 **동적 배열** 컨테이너
- `#include <vector>` 필요
- 일반 배열과 달리 크기가 **자동으로 늘어남**
- 메모리 연속 보장 → 인덱스 접근 속도 O(1)
- 끝에서의 삽입/삭제 O(1), 중간 삽입/삭제 O(n)

```cpp
#include <iostream>
#include <vector>
using namespace std;
```

### 배열 vs vector 비교

|항목|일반 배열|`vector`|
|---|---|---|
|크기|고정 (컴파일 타임)|동적 (런타임 변경 가능)|
|메모리|스택 (정적)|힙 (동적 자동 관리)|
|인덱스 접근|O(1)|O(1)|
|끝 삽입/삭제|불가|O(1)|
|중간 삽입/삭제|불가|O(n)|
|크기 확인|`sizeof` 필요|`.size()`|
|범위 검사|없음|`.at()` 사용 시 있음|

---

## 🔧 선언 및 초기화

```cpp
// 기본 선언 (빈 벡터)
vector<int>    v1;
vector<double> v2;
vector<string> v3;

// 크기 지정 (0으로 초기화)
vector<int> v4(5);          // {0, 0, 0, 0, 0}

// 크기 + 초기값 지정
vector<int> v5(5, 3);       // {3, 3, 3, 3, 3}

// 초기화 리스트
vector<int> v6 = {1, 2, 3, 4, 5};
vector<int> v7{10, 20, 30};

// 다른 벡터로 복사
vector<int> v8(v6);         // v6의 복사본
vector<int> v9 = v6;        // 동일

// 범위로 초기화 (반복자 사용)
vector<int> v10(v6.begin(), v6.begin() + 3);  // {1, 2, 3}

// 배열로 초기화
int arr[] = {4, 5, 6};
vector<int> v11(arr, arr + 3);  // {4, 5, 6}
```

---

## 📥 원소 추가 및 삽입

### 끝에 추가

```cpp
vector<int> v = {1, 2, 3};

v.push_back(4);        // {1, 2, 3, 4}
v.push_back(5);        // {1, 2, 3, 4, 5}

// emplace_back: 객체를 직접 생성하여 추가 (push_back보다 효율적)
v.emplace_back(6);     // {1, 2, 3, 4, 5, 6}
```

### 중간에 삽입 (insert)

```cpp
vector<int> v = {1, 2, 4, 5};

// 특정 위치에 삽입 (반복자 사용)
v.insert(v.begin() + 2, 3);          // {1, 2, 3, 4, 5}

// 특정 위치에 n개 삽입
v.insert(v.begin() + 1, 2, 99);      // {1, 99, 99, 2, 3, 4, 5}

// 다른 벡터 범위 삽입
vector<int> src = {10, 20};
v.insert(v.begin(), src.begin(), src.end());  // 앞에 {10, 20} 삽입
```

---

## 📤 원소 삭제

### 끝 원소 삭제

```cpp
vector<int> v = {1, 2, 3, 4, 5};

v.pop_back();          // {1, 2, 3, 4}  - 마지막 원소 삭제
```

### 특정 위치/범위 삭제 (erase)

```cpp
vector<int> v = {1, 2, 3, 4, 5};

v.erase(v.begin() + 2);              // {1, 2, 4, 5}  - 3번째 원소(3) 삭제
v.erase(v.begin() + 1, v.begin() + 3); // {1, 4, 5}  - 1~2번째 원소 삭제
// ⚠️ erase(first, last) : [first, last) 범위 삭제 (last는 미포함)
```

### 전체 삭제

```cpp
v.clear();             // 모든 원소 삭제, size = 0
                       // ⚠️ capacity는 유지됨
```

### 조건부 삭제 (remove + erase 패턴)

```cpp
#include <algorithm>
vector<int> v = {1, 3, 2, 3, 4, 3};

// 값이 3인 원소 모두 삭제 (erase-remove idiom)
v.erase(remove(v.begin(), v.end(), 3), v.end());
// {1, 2, 4}

// 조건에 맞는 원소 삭제
v.erase(remove_if(v.begin(), v.end(),
    [](int x) { return x % 2 == 0; }),  // 짝수 삭제
    v.end());
```

---

## 🔍 원소 접근

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// 인덱스 접근 (범위 검사 없음 - 빠름)
int a = v[0];          // 10
int b = v[4];          // 50

// at() (범위 검사 있음 - 안전, 초과 시 out_of_range 예외)
int c = v.at(2);       // 30
// v.at(10);           // 예외 발생!

// 처음/끝 원소
int f = v.front();     // 10 - 첫 번째 원소
int e = v.back();      // 50 - 마지막 원소

// 내부 배열 포인터 (C 스타일 호환)
int* ptr = v.data();   // 배열 첫 번째 원소 포인터
```

---

## 📏 크기와 용량

```cpp
vector<int> v = {1, 2, 3};

// 크기 관련
v.size();         // 3 - 현재 원소 개수
v.empty();        // false - 비어있으면 true

// 용량 관련
v.capacity();     // 현재 할당된 메모리 크기 (size보다 크거나 같음)
v.max_size();     // 이론상 최대 저장 가능 원소 수

// 용량 예약 (메모리 미리 확보 → push_back 반복 시 효율적)
v.reserve(100);   // capacity를 100으로 확보 (size는 변화 없음)

// 크기 조정
v.resize(5);      // size를 5로 변경, 늘어난 부분은 0으로 초기화
v.resize(5, 99);  // size를 5로 변경, 늘어난 부분은 99로 초기화
v.resize(2);      // size를 2로 줄임 (뒤의 원소 삭제)

// 불필요한 capacity 반환
v.shrink_to_fit(); // capacity를 size에 맞게 줄임
```

### size vs capacity 개념

```
push_back 반복 시 내부 동작:
  size     : 실제 원소 개수
  capacity : 할당된 메모리 공간

  v = {}          → size=0, capacity=0
  push_back(1)    → size=1, capacity=1
  push_back(2)    → size=2, capacity=2
  push_back(3)    → size=3, capacity=4  (보통 2배 확장)
  push_back(4)    → size=4, capacity=4
  push_back(5)    → size=5, capacity=8  (또 2배 확장)

→ 미리 reserve() 로 capacity 확보하면 재할당 비용 절감
```

---

## 🔁 반복자 (Iterator)

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// 정방향 반복자
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";    // 1 2 3 4 5
}

// 역방향 반복자
for (auto it = v.rbegin(); it != v.rend(); ++it) {
    cout << *it << " ";    // 5 4 3 2 1
}

// 범위 기반 for (가장 간단)
for (int x : v) {
    cout << x << " ";
}

// 참조로 받아 수정 가능
for (int& x : v) {
    x *= 2;                // 원소를 2배로 변경
}

// const 반복자 (읽기 전용)
for (auto it = v.cbegin(); it != v.cend(); ++it) {
    cout << *it;
}
```

---

## 🔄 정렬 및 탐색

```cpp
#include <algorithm>
vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};

// 오름차순 정렬
sort(v.begin(), v.end());             // {1, 1, 2, 3, 4, 5, 6, 9}

// 내림차순 정렬
sort(v.begin(), v.end(), greater<int>());  // {9, 6, 5, 4, 3, 2, 1, 1}

// 커스텀 정렬
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;    // 내림차순
});

// 이진 탐색 (정렬된 상태에서만 사용)
sort(v.begin(), v.end());
bool found = binary_search(v.begin(), v.end(), 4);  // true

// 선형 탐색 (find)
auto it = find(v.begin(), v.end(), 5);
if (it != v.end()) {
    cout << "위치: " << (it - v.begin());
}

// 최댓값/최솟값
auto maxIt = max_element(v.begin(), v.end());
auto minIt = min_element(v.begin(), v.end());
cout << *maxIt << " " << *minIt;

// 원소 개수 세기
int cnt = count(v.begin(), v.end(), 1);  // 1의 개수

// 조건에 맞는 원소 개수
int cnt2 = count_if(v.begin(), v.end(), [](int x) { return x > 3; });

// 합계
#include <numeric>
int sum = accumulate(v.begin(), v.end(), 0);  // 초기값 0부터 누적 합
```

---

## 🧩 2차원 vector

```cpp
// 2차원 벡터 선언
vector<vector<int>> matrix;

// 3행 4열, 0으로 초기화
vector<vector<int>> mat(3, vector<int>(4, 0));

// 직접 초기화
vector<vector<int>> grid = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// 원소 접근
int val = grid[1][2];    // 6 (1행 2열)
grid[0][0] = 99;

// 행 추가
grid.push_back({10, 11, 12});

// 순회
for (int i = 0; i < grid.size(); i++) {
    for (int j = 0; j < grid[i].size(); j++) {
        cout << grid[i][j] << " ";
    }
    cout << "\n";
}

// 범위 기반 for
for (auto& row : grid) {
    for (int val : row) {
        cout << val << " ";
    }
    cout << "\n";
}
```

---

## 📌 자주 쓰는 패턴 모음

### 중복 제거

```cpp
vector<int> v = {3, 1, 2, 1, 3, 2};

sort(v.begin(), v.end());             // 먼저 정렬
v.erase(unique(v.begin(), v.end()), v.end());
// {1, 2, 3}
```

### 벡터 뒤집기

```cpp
vector<int> v = {1, 2, 3, 4, 5};
reverse(v.begin(), v.end());          // {5, 4, 3, 2, 1}
```

### 두 벡터 합치기

```cpp
vector<int> a = {1, 2, 3};
vector<int> b = {4, 5, 6};

a.insert(a.end(), b.begin(), b.end()); // a = {1, 2, 3, 4, 5, 6}
```

### 벡터 복사

```cpp
vector<int> src = {1, 2, 3};
vector<int> dst;

// 방법 1: 대입
dst = src;

// 방법 2: assign
dst.assign(src.begin(), src.end());

// 방법 3: copy (algorithm)
dst.resize(src.size());
copy(src.begin(), src.end(), dst.begin());
```

### 특정 값으로 채우기

```cpp
vector<int> v(5);
fill(v.begin(), v.end(), 7);    // {7, 7, 7, 7, 7}
```

---

## ⚠️ 핵심 정리 & 자주 하는 실수

### 주요 멤버함수 한눈에 보기

| 함수                      | 설명              | 시간복잡도  |
| ----------------------- | --------------- | ------ |
| `v.push_back(x)`        | **끝에 원소 추가**    | $O(1)$ |
| `v.pop_back()`          | **끝 원소 삭제**     | $O(1)$ |
| `v.insert(it, x)`       | **it 위치에 삽입**   | $O(n)$ |
| `v.erase(it)`           | **it 위치 삭제**    | $O(n)$ |
| `v.clear()`             | **전체 삭제**       | $O(n)$ |
| `v.size()`              | **원소 개수**       | $O(1)$ |
| `v.empty()`             | **빈 벡터 확인**     | $O(1)$ |
| `v.resize(n)`           | **크기 변경**       | $O(n)$ |
| `v.reserve(n)`          | **용량 예약**       | $O(n)$ |
| `v.front()`             | **첫 번째 원소**     | $O(1)$ |
| `v.back()`              | **마지막 원소**      | $O(1)$ |
| `v[i]`                  | **i번째 원소 (빠름)** | $O(1)$ |
| `v.at(i)`               | **i번째 원소 (안전)** | $O(1)$ |
| `v.begin()` / `v.end()` | **반복자**         | $O(1)$ |
| `v.data()`              | **내부 배열 포인터**   | $O(1)$ |

### 자주 하는 실수

> ⚠️ `erase(first, last)` 에서 **last는 미포함**: `[first, last)` 범위임 ⚠️ `v[i]` 는 범위 검사 없음 → 범위 초과 시 undefined behavior, 안전하게 쓰려면 `v.at(i)` ⚠️ `clear()` 는 size만 0으로 만들고 **capacity는 유지** → 메모리 반환하려면 `shrink_to_fit()` 추가 ⚠️ `remove()` 는 원소를 실제로 삭제하지 않음 → 반드시 `erase()`와 함께 사용 (erase-remove idiom) ⚠️ 반복 중 `erase()` 사용 시 반복자 **무효화** 주의 → `erase()`의 반환값(다음 반복자)을 사용 ⚠️ `size()`의 반환형은 `size_t` (부호 없는 정수) → `v.size() - 1` 에서 size가 0이면 **언더플로우** ⚠️ `push_back` 반복 시 재할당 발생 → 크기를 미리 알면 **`reserve()`** 로 성능 개선

### 반복 중 안전한 erase 패턴

```cpp
vector<int> v = {1, 2, 3, 4, 5};

// ❌ 잘못된 방법 (반복자 무효화)
for (auto it = v.begin(); it != v.end(); ++it) {
    if (*it % 2 == 0) v.erase(it);  // it 무효화!
}

// ✅ 올바른 방법 (반환값 사용)
for (auto it = v.begin(); it != v.end(); ) {
    if (*it % 2 == 0)
        it = v.erase(it);    // erase 후 다음 유효 반복자 반환
    else
        ++it;
}
```