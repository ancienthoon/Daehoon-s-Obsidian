# 📘 C++ string 클래스

#cpp #string #STL #문자열

---

## 📋 목차

- [[#🧩 string 클래스란?]]
- [[#🔧 string 선언 및 초기화]]
- [[#➕ 문자열 연결과 비교]]
- [[#🔍 문자열 탐색]]
- [[#✂️ 문자열 조작]]
- [[#📏 크기와 용량]]
- [[#🔄 변환 함수]]
- [[#🔁 반복자(Iterator)]]
- [[#⚠️ 핵심 정리 & 자주 하는 실수]]

---

## 🧩 string 클래스란?

- C++ 표준 라이브러리(STL)에서 제공하는 **문자열 전용 클래스**
- `#include <string>` 필요
- C 스타일 문자배열(`char[]`)의 불편함을 해결
    - 길이 자동 관리, 메모리 자동 해제
    - 연산자(`+`, `==`, `<` 등) 직접 사용 가능

```cpp
#include <iostream>
#include <string>
using namespace std;
```

### C 스타일 vs string 비교

|항목|`char[]` (C 스타일)|`string` (C++)|
|---|---|---|
|선언|`char s[100]`|`string s`|
|복사|`strcpy(s, t)`|`s = t`|
|연결|`strcat(s, t)`|`s + t`|
|비교|`strcmp(s, t)`|`s == t`|
|길이|`strlen(s)`|`s.length()`|
|메모리|직접 관리|자동 관리|

---

## 🔧 string 선언 및 초기화

```cpp
string s1;                    // 빈 문자열
string s2 = "Hello";          // 문자열 리터럴로 초기화
string s3("World");           // 생성자로 초기화
string s4(s2);                // 복사 초기화
string s5(5, 'A');            // "AAAAA" - 문자 n개로 초기화
string s6 = s2 + " " + s3;   // 연결하여 초기화 → "Hello World"

// C 스타일 문자열과 혼용
const char* cstr = "Hi";
string s7 = cstr;             // char* → string 자동 변환
const char* back = s7.c_str(); // string → char* 변환
```

---

## ➕ 문자열 연결과 비교

### 연결 (concatenation)

```cpp
string a = "Hello";
string b = "World";

string c = a + " " + b;   // "Hello World"
a += "!";                  // a = "Hello!"
a.append(" C++");          // a = "Hello! C++"
a.push_back('?');          // 맨 뒤에 문자 1개 추가
```

### 비교

```cpp
string s1 = "apple";
string s2 = "banana";

// 연산자 사용 (사전순 비교)
s1 == s2    // false
s1 != s2    // true
s1 < s2     // true  ('a' < 'b')
s1 > s2     // false

// compare() 함수
// 반환값: 0이면 같음, 음수면 s1 < s2, 양수면 s1 > s2
int result = s1.compare(s2);

// 부분 비교
s1.compare(0, 3, s2);       // s1의 0번째부터 3글자를 s2와 비교
s1.compare(0, 3, s2, 0, 3); // 둘 다 부분 비교
```

---

## 🔍 문자열 탐색

### 특정 위치 문자 접근

```cpp
string s = "Hello";

char c1 = s[1];          // 'e' - 범위 검사 없음
char c2 = s.at(1);       // 'e' - 범위 초과 시 예외 발생
char c3 = s.front();     // 'H' - 첫 번째 문자
char c4 = s.back();      // 'o' - 마지막 문자
```

### find() - 문자/문자열 찾기

```cpp
string s = "Hello World Hello";

// 기본 탐색 (앞에서부터)
size_t pos = s.find("Hello");       // 0 반환 (첫 번째 위치)
size_t pos2 = s.find("Hello", 1);   // 1번 위치부터 탐색 → 12 반환
size_t pos3 = s.find('o');          // 4 반환

// 찾지 못한 경우
if (s.find("xyz") == string::npos) {
    cout << "찾지 못함" << endl;
}
// string::npos = 찾기 실패를 나타내는 상수 (보통 -1 또는 최대값)

// 뒤에서부터 탐색
size_t pos4 = s.rfind("Hello");     // 12 반환 (마지막 위치)

// 문자 집합 중 하나라도 있는 위치 탐색
size_t pos5 = s.find_first_of("aeiou");  // 'e'의 위치 1 반환
size_t pos6 = s.find_last_of("aeiou");   // 마지막 모음 위치

// 문자 집합에 없는 첫 위치
size_t pos7 = s.find_first_not_of("Helo"); // ' '의 위치 5 반환
```

---

## ✂️ 문자열 조작

### substr() - 부분 문자열 추출

```cpp
string s = "Hello World";

string sub1 = s.substr(6);        // "World"      (6번 인덱스부터 끝까지)
string sub2 = s.substr(0, 5);     // "Hello"      (0번부터 5글자)
string sub3 = s.substr(6, 3);     // "Wor"        (6번부터 3글자)
```

### insert() - 삽입

```cpp
string s = "Hello World";

s.insert(5, ",");           // "Hello, World"   (5번 위치에 삽입)
s.insert(0, ">>>");         // ">>>Hello, World"
s.insert(5, 3, '!');        // 5번 위치에 '!' 3개 삽입
```

### erase() - 삭제

```cpp
string s = "Hello World";

s.erase(5, 6);      // "Hello"        (5번 위치부터 6글자 삭제)
s.erase(5);         // "Hello"        (5번 위치부터 끝까지 삭제)
s.erase();          // ""             (전체 삭제, clear()와 동일)
```

### replace() - 교체

```cpp
string s = "Hello World";

s.replace(6, 5, "C++");    // "Hello C++"   (6번부터 5글자를 "C++"으로 교체)
s.replace(0, 5, "Hi");     // "Hi C++"
```

### 대소문자 변환 (algorithm 헤더 필요)

```cpp
#include <algorithm>
string s = "Hello World";

// 소문자로
transform(s.begin(), s.end(), s.begin(), ::tolower);
// "hello world"

// 대문자로
transform(s.begin(), s.end(), s.begin(), ::toupper);
// "HELLO WORLD"
```

---

## 📏 크기와 용량

```cpp
string s = "Hello";

s.length();     // 5 - 문자열 길이 (size()와 동일)
s.size();       // 5
s.empty();      // false - 빈 문자열이면 true
s.clear();      // 문자열 비우기 → s = ""

// 용량 관련
s.capacity();        // 현재 할당된 메모리 크기
s.max_size();        // 최대 저장 가능 크기
s.reserve(100);      // 최소 100 크기 메모리 미리 확보
s.shrink_to_fit();   // 불필요한 메모리 반환
```

---

## 🔄 변환 함수

### string ↔ 숫자 변환

```cpp
// 숫자 → string
string s1 = to_string(42);       // "42"
string s2 = to_string(3.14);     // "3.140000"

// string → 숫자
string num = "123";
int    i = stoi(num);            // 123 (string to int)
long   l = stol(num);            // 123 (string to long)
float  f = stof("3.14");         // 3.14 (string to float)
double d = stod("3.14159");      // 3.14159 (string to double)

// 변환 불가한 경우 예외 발생 (invalid_argument)
// 범위 초과인 경우 예외 발생 (out_of_range)
```

### string ↔ char* 변환

```cpp
string s = "Hello";

// string → const char*
const char* cptr = s.c_str();    // C 스타일 포인터 반환
// ⚠️ s가 변경되면 cptr이 가리키는 내용도 무효화될 수 있음

// string → char 배열 복사
char buf[20];
s.copy(buf, s.size());           // buf에 복사 (null 종료 문자 없음)
buf[s.size()] = '\0';            // 직접 null 추가 필요
```

---

## 🔁 반복자 (Iterator)

```cpp
string s = "Hello";

// 정방향 반복자
for (auto it = s.begin(); it != s.end(); ++it) {
    cout << *it;                 // H e l l o
}

// 역방향 반복자
for (auto it = s.rbegin(); it != s.rend(); ++it) {
    cout << *it;                 // o l l e H
}

// 범위 기반 for (가장 간단)
for (char c : s) {
    cout << c;
}

// 인덱스 기반
for (int i = 0; i < s.size(); i++) {
    cout << s[i];
}
```

---

## 📌 자주 쓰는 패턴 모음

### 문자열 분리 (split)

```cpp
// 구분자로 문자열 분리
string s = "apple,banana,cherry";
vector<string> tokens;
string token;
stringstream ss(s);   // #include <sstream>

while (getline(ss, token, ',')) {
    tokens.push_back(token);
}
// tokens = {"apple", "banana", "cherry"}
```

### 공백 제거 (trim)

```cpp
string s = "  Hello World  ";

// 앞 공백 제거
s.erase(0, s.find_first_not_of(' '));

// 뒤 공백 제거
s.erase(s.find_last_not_of(' ') + 1);
```

### 특정 문자 전체 교체

```cpp
string s = "Hello World";
// 'l' 전체를 'L'로 교체
replace(s.begin(), s.end(), 'l', 'L');
// "HeLLo WorLd"
```

### 문자열 내 특정 부분 문자열 전체 교체

```cpp
string s = "aabbcc";
string from = "bb";
string to = "XX";

size_t pos = 0;
while ((pos = s.find(from, pos)) != string::npos) {
    s.replace(pos, from.size(), to);
    pos += to.size();
}
// "aaXXcc"
```

---

## ⚠️ 핵심 정리 & 자주 하는 실수

### 주요 멤버함수 한눈에 보기

| 함수                        | 설명                | 반환형           |
| ------------------------- | ----------------- | ------------- |
| `s.length()` / `s.size()` | 문자열 길이            | `size_t`      |
| `s.empty()`               | 비어있으면 true        | `bool`        |
| `s.clear()`               | 문자열 비우기           | `void`        |
| `s.at(i)`                 | i번째 문자 (범위 검사)    | `char&`       |
| `s[i]`                    | i번째 문자 (범위 검사 없음) | `char&`       |
| `s.front()` / `s.back()`  | 첫/마지막 문자          | `char&`       |
| `s.find(t)`               | t의 위치 탐색          | `size_t`      |
| `s.rfind(t)`              | t를 뒤에서 탐색         | `size_t`      |
| `s.substr(pos, n)`        | pos부터 n개 추출       | `string`      |
| `s.insert(pos, t)`        | pos에 t 삽입         | `string&`     |
| `s.erase(pos, n)`         | pos부터 n개 삭제       | `string&`     |
| `s.replace(pos, n, t)`    | pos부터 n개를 t로 교체   | `string&`     |
| `s.append(t)`             | 뒤에 t 추가           | `string&`     |
| `s.compare(t)`            | 사전순 비교            | `int`         |
| `s.c_str()`               | C 스타일 포인터 반환      | `const char*` |
| `to_string(n)`            | 숫자 → string       | `string`      |
| `stoi(s)`                 | string → int      | `int`         |

### 자주 하는 실수

> ⚠️ `find()` 실패 시 반환값은 `-1`이 아닌 **`string::npos`** 로 비교해야 함 ⚠️ `s[i]` 는 범위 검사 없음 → 인덱스 초과 시 undefined behavior, 안전하게 쓰려면 `s.at(i)` ⚠️ `substr(pos, n)` 의 두 번째 인자는 **길이(n)** 이지 끝 인덱스가 아님 ⚠️ `c_str()` 로 얻은 포인터는 string 변경 시 **무효화** 될 수 있음 → 즉시 사용하거나 복사 ⚠️ `stoi()`, `stod()` 등은 변환 불가 문자열 입력 시 **예외** 발생 → try-catch 처리 권장 ⚠️ `size_t`는 부호 없는 정수 → `s.size() - 1` 에서 size가 0이면 **언더플로우** 발생


[[문자열 파싱]]