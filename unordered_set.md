# ⚡ C++ STL: unordered_set (정렬되지 않은 집합)

> [!abstract] **핵심 정의**
> 
> - **정렬 안 됨:** 원소들이 들어간 순서나 크기와 상관없이 해시 함수에 의해 임의의 위치에 저장됩니다.
>     
> - **매우 빠름:** 탐색, 삽입, 삭제의 평균 시간 복잡도가 **$O(1)$**입니다.
>     
> - **자료구조:** **해시 테이블(Hash Table)**을 사용하여 구현됩니다.
>     

---

## 1. set vs unordered_set 비교

| **특성**     | **std::set**           | **std::unordered_set**     |
| ---------- | ---------------------- | -------------------------- |
| **내부 구조**  | Red-Black Tree (이진 트리) | **Hash Table (해시 테이블)**    |
| **시간 복잡도** | $O(\log N)$            | **평균 $O(1)$** (최악 $O(N)$)  |
| **정렬 여부**  | 항상 오름차순 정렬됨            | **정렬되지 않음**                |
| **사용 시점**  | 데이터가 순서대로 정렬되어야 할 때    | **정렬 필요 없이 빠른 탐색이 최우선일 때** |

---

## 2. 주요 멤버 함수

사용법은 `std::set`과 거의 동일하지만, 내부적으로 정렬을 하지 않는다는 차이만 있습니다.

- `insert(key)`: 원소 삽입 (이미 있으면 무시)
    
- `erase(key)`: 원소 삭제
    
- `find(key)`: 원소 탐색 (못 찾으면 `end()` 반환)
    
- `count(key)`: 원소가 존재하면 1, 없으면 0 반환
    

---

## 💻 실전 코드 예시

```cpp
#include <iostream>
#include <unordered_set>
#include <string>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    unordered_set<string> us;

    // 데이터 삽입
    us.insert("apple");
    us.insert("banana");
    us.insert("cherry");
    us.insert("apple"); // 중복 삽입 무시

    // 전체 원소 출력 (출력 순서는 매번 다를 수 있음)
    cout << "원소 목록: ";
    for (auto item : us) {
        cout << item << " ";
    }
    cout << "\n";

    // 탐색 예시
    string target = "banana";
    if (us.find(target) != us.end()) {
        cout << target << "을(를) 찾았습니다.\n";
    }

    return 0;
}
```

---

## 🎓 편입 면접 대비: 핵심 질문

> [!question] **`unordered_set`의 탐색 속도가 $O(1)$인 원리는 무엇인가요?**
> 
> **Answer:** "해시 함수를 통해 데이터(Key)를 특정 인덱스 숫자로 즉시 변환하기 때문입니다. 배열의 인덱스를 찾아가는 것처럼 데이터의 위치를 바로 계산해낼 수 있어 데이터 양에 상관없이 일정한 속도를 낼 수 있습니다."

> [!question] **`unordered_set`에서 최악의 경우 $O(N)$이 발생하는 이유는?**
> 
> **Answer:** "**해시 충돌(Hash Collision)** 때문입니다. 서로 다른 데이터가 같은 해시 값을 가져 한 버킷(Bucket)에 몰리게 되면, 그 안에서 데이터를 일일이 찾아야 하므로 성능이 저하될 수 있습니다."
