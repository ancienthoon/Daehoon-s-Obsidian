
# 🏗️ [C++] 크기 미정인 2차원 벡터 다루기

[!info] **핵심 원리**
 
1. 처음에는 **텅 빈 상자**(`vector<vector<int>> v;`)만 만듭니다.
    
2. 데이터를 넣고 싶을 때마다 새로운 행(벡터)을 만들어 **`push_back()`**으로 집어넣습니다.
    


## 1. 선언 및 동적 추가 (기본)

처음에 행과 열의 크기를 전혀 몰라도 아래와 같이 구현할 수 있습니다.

C++

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    // 1. 크기가 전혀 정해지지 않은 2차원 벡터 선언
    vector<vector<int>> v;

    // 2. 새로운 '행'을 만들어서 추가하기
    vector<int> row1 = {10, 20}; 
    v.push_back(row1); // 1행 생성 -> {{10, 20}}

    // 3. 기존 행의 끝에 데이터 추가하기
    v[0].push_back(30); // 1행의 끝에 추가 -> {{10, 20, 30}}

    // 4. 새로운 행을 즉석에서 추가하기
    v.push_back({40, 50, 60, 70}); // 2행 생성 (크기가 달라도 됨!)

    return 0;
}
```

---

## 2. 실전 활용: 입력받은 만큼만 칸 만들기

보통 알고리즘 문제에서는 "N개의 줄에 걸쳐 숫자들이 들어온다"고 할 때 이렇게 많이 씁니다.

```cpp
int n; // 행의 개수
cin >> n;

vector<vector<int>> v(n); // 행의 개수만 미리 예약 (열은 아직 미정)

for(int i = 0; i < n; i++) {
    int m; // i번째 행에 들어올 숫자의 개수
    cin >> m;
    for(int j = 0; j < m; j++) {
        int val;
        cin >> val;
        v[i].push_back(val); // 입력받는 대로 열 크기가 늘어남
    }
}
```


# 벡터, 동적배열 차이점(1차원)

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int size = 5;

    // [방법 1: new 사용]
    int* arr = new int[size]; // 땅을 빌립니다.
    arr[0] = 10;
    // ... 사용 중 ...
    delete[] arr; // 다 썼으면 반드시 수동으로 반납해야 합니다! (중요)

    // [방법 2: vector 사용]
    vector<int> v(size); // 스마트 가방을 삽니다.
    v[0] = 10;
    v.push_back(20); // 크기가 모자라면 알아서 늘어납니다.
    
    // 함수가 끝나면 v는 자동으로 사라지며 메모리를 반납합니다.
    return 0;
}
```

# 2차원이다

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);

    int rows = 2, cols = 3;

    // 1. 일반 2차원 배열
    int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};

    // 2. 벡터 (크기 미정 상태에서 push_back)
    // "필요할 때마다 칸을 하나씩 붙여나가는 포스트잇"
    vector<vector<int>> v1;
    for (int i = 0; i < rows; i++) {
        vector<int> temp;
        for (int j = 0; j < cols; j++) {
            temp.push_back(j + 1);
        }
        v1.push_back(temp);
    }

    // 3. 벡터 (크기 정해진 상태에서 v[i][j]로 입력)
    // "미리 칸을 다 만들어둔 스마트 보관함"
    vector<vector<int>> v2(rows, vector<int>(cols));
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            v2[i][j] = j + 1;
        }
    }

    // 4. 동적 배열 (new 사용)
    // "땅을 직접 빌려서 칸을 만드는 수동 작업"
    int** dArr = new int*[rows];
    for (int i = 0; i < rows; i++) {
        dArr[i] = new int[cols];
        for (int j = 0; j < cols; j++) {
            dArr[i][j] = j + 1;
        }
    }

    // [정리: 동적 배열은 반드시 수동으로 치워야 함]
    for (int i = 0; i < rows; i++) delete[] dArr[i];
    delete[] dArr;

    return 0;
}
```