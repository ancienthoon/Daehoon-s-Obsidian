& : 이 상자의 주소
" * " : 이 주소에 적힌 곳으로 가서 그 안에 있는 값 

```cpp
#include <iostream>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int a = 10;      // 'a'라는 이름의 상자에 10을 넣음
    int* ptr = &a;   // 'ptr'이라는 주소 메모지에 a상자의 주소를 적음

    cout << "a의 값: " << a << "\n";
    cout << "a의 주소 (&a): " << &a << "\n";
    cout << "ptr에 저장된 주소: " << ptr << "\n";
    cout << "ptr이 가리키는 곳의 값 (*ptr): " << *ptr << "\n";

    // 포인터를 이용해서 a의 값을 바꿔볼까요?
    *ptr = 20; 

    cout << "바뀐 a의 값: " << a << "\n";

    return 0;
}
```

벡터와 포인터의 차이

```cpp
#include <iostream>
#include <vector>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    vector<int> v = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

    // 1. 우리가 보통 쓰는 방식
    int val1 = v[4]; 

    // 2. 포인터를 이용한 방식 (내부 원리)
    // v.data()는 벡터의 첫 번째 칸 주소를 알려줘요.
    int* ptr = v.data(); 
    int val2 = *(ptr + 4); // 주소에서 4칸 이동한 뒤, 상자를 열어라(*)

    cout << "v[4]의 값: " << val1 << "\n";
    cout << "*(ptr + 4)의 값: " << val2 << "\n";

    return 0;
}
```

여기부터 참조자
```cpp
#include <iostream>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int realname = 100;
    int &nickname = realname; // nickname은 이제 realname의 별명입니다.

    nickname = 200; // 별명을 통해 값을 바꿨는데...

    cout << "본명(realname)의 값: " << realname << "\n"; // 진짜 이름의 값도 200으로 변함!
    cout << "별명(nickname)의 값: " << nickname << "\n";

    return 0;
}
```


## 포인터와 참조자의 차이점

| **특징**    | **포인터 (int* ptr)**    | **참조자 (int& ref)**         |
| --------- | --------------------- | -------------------------- |
| **정체**    | 주소를 저장하는 **별도의 상자**   | 기존 변수에 붙인 **새로운 이름**       |
| **선언 방식** | `int* ptr = &a;`      | `int& ref = a;`            |
| **빈 상태**  | `nullptr` (비어있기) 가능   | **불가능** (반드시 주인이 있어야 함)    |
| **환승**    | 중간에 다른 변수 주소로 바꿀 수 있음 | **불가능** (한 번 정해지면 끝까지 한 몸) |
| **기호 사용** | `*`, `->`, `&` 등 복잡함  | 그냥 일반 변수처럼 사용해서 **매우 편리함** |


동적배열(new)과 ptr 관계

```cpp
#include <iostream>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int size;
    cin >> size; // 사용자가 원하는 크기를 입력받음

    // 관리실(new)에 가서 size만큼 칸을 빌리고, 시작 주소를 ptr에 저장!
    int* ptr = new int[size]; 

    for (int i = 0; i < size; i++) {
        ptr[i] = i + 1; // 포인터지만 배열처럼 쓸 수 있어요!
    }

    // 다 썼으면 반납해야 해요. (매우 중요!)
    delete[] ptr; 

    return 0;
}
```


# 📦 1. 동적 배열 (Dynamic Array)

> [!info] **핵심 정의**
> 
> 동적 배열은 프로그램이 실행되는 도중(**Runtime**)에 내가 원하는 만큼 메모리를 빌려오고(`new`), 다 쓰면 다시 돌려주는(`delete`) 방식입니다. 일반 배열은 학교 사물함처럼 크기가 딱 정해져 있지만, 동적 배열은 필요할 때마다 늘리거나 줄일 수 있는 '마법 주머니'와 같습니다.

### ✅ 동적 배열 기본 코드

```cpp
#include <iostream>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int n;
    cin >> n; // 실행 중에 크기를 결정함

    // 1. 메모리 빌리기 (할당)
    int* arr = new int[n];

    for (int i = 0; i < n; i++) 
    {
        arr[i] = i * 10;
    }

    for (int i = 0; i < n; i++) 
    {
        cout << arr[i] << " ";
    }

    // 2. 메모리 돌려주기 (해제) - 매우 중요!
    delete[] arr; 

    return 0;
}
```


# 📦 1. 동적 배열 (Dynamic Array) - 사이즈 변경

> [!info] **배열 크기 변경의 4단계 (이사 가기)**
> 
> 1. **새 집 구하기:** 더 큰 새로운 배열을 만듭니다.
>     
> 2. **짐 옮기기:** 기존 배열의 데이터를 새 배열로 복사합니다.
>     
> 3. **헌 집 부수기:** 기존 배열을 메모리에서 삭제(`delete`)합니다.
>     
> 4. **주소 업데이트:** 내 포인터 변수가 새 배열의 주소를 가리키게 합니다.
>     

### ✅ 배열 사이즈 변경 코드

```cpp
#include <iostream>

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int size = 3;
    int* arr = new int[size];

    // 초기 값 대입
    for (int i = 0; i < size; i++) arr[i] = i + 1;

    cout << "변경 전: ";
    for (int i = 0; i < size; i++) cout << arr[i] << " ";
    cout << "\n";

    // --- 사이즈를 5로 늘리기 ---
    int new_size = 5;
    int* temp = new int[new_size]; // 1. 새 집 구하기

    for (int i = 0; i < size; i++) 
    {
        temp[i] = arr[i];          // 2. 짐 옮기기
    }

    delete[] arr;                  // 3. 헌 집 부수기 (메모리 해제)
    arr = temp;                    // 4. 주소 업데이트 (이제 arr은 새 집을 가리킴)
    size = new_size;

    // 늘어난 공간에 값 채우기
    arr[3] = 4; arr[4] = 5;

    cout << "변경 후: ";
    for (int i = 0; i < size; i++) cout << arr[i] << " ";

    delete[] arr; // 최종 해제
    return 0;
}
```

---

# 📞 2. 함수 호출 방식 3가지 (Call by ...)

함수에 데이터를 넘길 때 **"원본을 바꿀 수 있느냐"**가 핵심입니다. 각각의 결과값이 다르게 나오는 코드로 비교해 드릴게요.

### 📊 한눈에 비교하기

|**방식**|**설명**|**비유**|**원본 변경**|
|---|---|---|---|
|**Value**|값을 복사해서 전달|사진을 복사해서 주기 (복사본에 낙서해도 원본은 멀쩡함)|**X**|
|**Reference**|변수에 별명을 붙여 전달|본명 대신 별명 부르기 (누굴 부르든 결국 그 사람임)|**O**|
|**Address**|주소지가 적힌 종이를 전달|집 주소 알려주기 (주소로 찾아가서 가구를 바꾸면 바뀜)|**O**|

### ✅ 결과가 다르게 나오는 비교 코드

```cpp
#include <iostream>

using namespace std;

// 1. Value: 값을 복사함
void call_value(int n) 
{
    n = 100;
}

// 2. Reference: 별명을 사용함 (& 이용)
void call_ref(int& n) 
{
    n = 200;
}

// 3. Address: 주소를 알려줌 (* 이용)
void call_addr(int* n) 
{
    *n = 300;
}

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int a = 10;

    call_value(a);
    cout << "Value 호출 후: " << a << "\n"; // 결과: 10 (안 바뀜)

    call_ref(a);
    cout << "Ref 호출 후: " << a << "\n";   // 결과: 200 (바뀜)

    call_addr(&a);
    cout << "Addr 호출 후: " << a << "\n";  // 결과: 300 (바뀜)

    return 0;
}
```

# Call by Reference vs Call by Address 결과 비교

> [!info] **결정적 차이점**
> 
> - **Reference:** "이건 네 별명이야."라고 정해주는 거라, **대상이 없으면 아예 함수를 부를 수도 없습니다.** (항상 결과 발생)
>     
> - **Address:** "이건 주소 적힌 쪽지야."라고 주는 거라, **빈 쪽지(NULL)를 줄 수도 있습니다.** (상황에 따라 결과가 안 변할 수 있음)
>     

### ✅ 비교 코드

이 코드는 **정상적인 데이터**를 보냈을 때와 **빈 데이터(NULL)**를 보냈을 때 두 방식이 어떻게 다르게 반응하는지 보여줍니다.

```cpp
#include <iostream>

using namespace std;

// 1. Reference: 별명을 부름. 대상이 무조건 있어야 함.
void add_ten_ref(int& n) {
    n += 10;
}

// 2. Address: 주소를 확인하고 찾아감. 주소가 비었으면(NULL) 아무것도 안 함.
void add_twenty_addr(int* n) {
    if (n != nullptr) {
        *n += 20;
    }
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    int a = 0;
    int b = 0;

    // --- 상황 1: 정상적인 전달 ---
    add_ten_ref(a);      // a는 10이 됨
    add_twenty_addr(&b); // b는 20이 됨

    cout << "상황 1 - Ref 결과: " << a << "\n";
    cout << "상황 1 - Addr 결과: " << b << "\n\n";

    // --- 상황 2: "아무것도 없음"을 전달하고 싶을 때 ---
    
    // add_ten_ref(nullptr); // [에러!] 참조자는 빈 값을 받을 수 없음 (컴파일 안 됨)
    
    int* ptr = nullptr;
    add_twenty_addr(ptr); // [정상] 빈 주소를 줘도 함수 내부 if문 덕분에 안전함

    cout << "상황 2 - Addr 결과: " << b << " (변화 없음)\n";

    return 0;
}
```

---

### 📊 왜 결과가 이렇게 다를까? (옵시디언 정리용)

1. **동작의 강제성:**
    
    - **Reference**는 함수가 실행되는 순간 **무조건** 원본 데이터가 존재해야 합니다. "값이 없는 상태"를 고려할 필요가 없어 코드가 깔끔하지만 유연성은 조금 떨어집니다.
        
    - **Address**는 "선택권"이 있습니다. 주소가 있으면 바꾸고, 없으면(`nullptr`) 안 바꿀 수 있습니다.
        
2. **안정성:**
    
    - **Reference**는 `NULL` 포인터 역참조 같은 끔찍한 에러(런타임 에러)를 문법적으로 막아줍니다.
        
    - **Address**는 개발자가 `if (n != nullptr)` 처리를 깜빡하면 프로그램이 죽어버릴 수 있는 위험이 있습니다.