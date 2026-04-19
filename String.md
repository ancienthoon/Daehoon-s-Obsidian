> [!info] **학습 가이드** `string`은 문자들을 한 줄로 세워놓은 기차와 같아요. 각 칸(인덱스)은 0번부터 시작한다는 점만 기억하면 님도 마스터할 수 있습니다!

### 1. 크기 및 상태 확인 (Size & Capacity)

가장 기본이 되는 함수들입니다.

- **`size()` / `length()`**: 문자열의 길이를 반환합니다. (둘은 똑같아요!)
    
- **`empty()`**: 비어있으면 `true`, 아니면 `false`를 반환합니다.
    
- **`clear()`**: 문자열의 모든 내용을 지웁니다.
    
- **`resize(n, 'str')`**: 크기를 `n`으로 바꾸고 그 사이를 'str'로 채웁니다. 더 커지면 빈칸으로 채워져요.
    

### 2. 문자 접근 (Access)

기차의 몇 번째 칸에 무엇이 있는지 확인합니다.

- **`at(index)`**: 해당 위치의 문자를 반환합니다. (범위를 벗어나면 에러를 알려줘서 안전해요!)
    
- **`operator[index]`**: 배열처럼 `s[0]` 형태로 접근합니다. (매우 빨라요!)
    
- **`front()`**: 맨 앞 문자를 가져옵니다.
    
- **`back()`**: 맨 뒤 문자를 가져옵니다.
    

### 3. 수정 및 추가 (Modify)

문자열을 붙이거나 자를 때 사용합니다.

- **`push_back(char)`**: 맨 뒤에 문자 하나를 추가합니다.
    
- **`pop_back()`**: 맨 뒤의 문자 하나를 제거합니다.
    
- **`append(str)`**: 문자열 뒤에 다른 문자열을 이어 붙입니다.
    
- **`insert(pos, str)`**: `pos` 위치에 문자열을 삽입합니다.
    
- **`erase(pos, len)`**: `pos`부터 `len`만큼 지웁니다.
    
- **`replace(pos, len, str)`**: 특정 범위를 다른 문자열로 바꿉니다.
    

### 4. 검색 및 부분 추출 (Search & Substring)

원하는 내용을 찾을 때 필수입니다.

- **`substr(pos, len)`**: `pos`부터 `len`만큼의 부분 문자열을 새로 만듭니다.
    
- **`find(str)`**: 특정 문자열이 시작되는 위치를 찾습니다. (못 찾으면 `string::npos` 반환)
    
- **`rfind(str)`**: 뒤에서부터 찾습니다.
    
- **`compare(str)`**: 문자열을 비교하여 같으면 0, 다르면 양수/음수를 반환합니다.
    

### 5. 변환 및 기타 (Conversion & Utility)

숫자와 문자열을 왔다 갔다 할 때 유용합니다.

- **`stoi(str)`**: 문자열을 **정수(int)**로 바꿉니다. (String To Integer)
    
- **`to_string(value)`**: 숫자형 데이터를 **문자열**로 바꿉니다.
    
- **`getline(cin, s)`**: 공백을 포함한 한 줄 전체를 입력받습니다.
    
- **`sort(s.begin(), s.end())`**: 알파벳 순서대로 정렬합니다. (`#include <algorithm>` 필요)

# 🔡 [C++] 대소문자 변환 함수 마스터

> [!info] **핵심 요약**
> 
> C++에서 대소문자를 다루는 함수는 크게 두 가지 라이브러리를 사용합니다.
> 
> 1. `<cctype>`: 문자 **하나**(`char`)를 처리할 때
>     
> 2. `<algorithm>`: 문자열 **전체**(`string`)를 한꺼번에 바꿀 때
>     

---

## 1. 문자 하나씩 바꾸기 (`char` 단위)

`#include <cctype>` 헤더가 필요합니다. 중학생도 이해하기 쉽게 이름이 아주 직관적이에요!

| **함수명**          | **역할**           | **결과 (예시)** |
| ---------------- | ---------------- | ----------- |
| **`toupper(c)`** | 소문자를 **대문자**로 변환 | 'a' → 'A'   |
| **`tolower(c)`** | 대문자를 **소문자**로 변환 | 'A' → 'a'   |
| **`isupper(c)`** | 대문자인지 확인 (bool)  | 'A' → true  |
| **`islower(c)`** | 소문자인지 확인 (bool)  | 'a' → true  |
| **`isalpha(c)`** | 알파벳인지 확인 (bool)  | '1' → false |

---

## 2. 문자열 전체 바꾸기 (`string` 단위)

문자열 전체를 한 번에 대문자나 소문자로 바꿀 때는 `transform` 함수를 사용하는 것이 가장 깔끔한 '최고의 방법'입니다.

```cpp
#include <iostream>
#include <string>
#include <algorithm> // transform 사용을 위해 필요
#include <cctype>    // toupper, tolower 사용을 위해 필요

using namespace std;

int main() 
{
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    string s = "Hello Obsidian";

    // 1. 전체를 대문자로 바꾸기
    // s.begin()부터 s.end()까지를 대상으로, 결과를 s.begin()부터 채우며, ::toupper를 적용하라!
    transform(s.begin(), s.end(), s.begin(), ::toupper);
    cout << "대문자: " << s << "\n"; // HELLO OBSIDIAN

    // 2. 전체를 소문자로 바꾸기
    transform(s.begin(), s.end(), s.begin(), ::tolower);
    cout << "소문자: " << s << "\n"; // hello obsidian

    return 0;
}
```

[[문자열 파싱]]

