# 파이썬 기초 — 변수, 자료형, 연산자, 조건문, 반복문

#프로그래밍 #파이썬 #입문

---

## 📌 목차

1. [[#1. 변수 (Variable)]]
2. [[#2. 자료형 (Data Type)]]
3. [[#3. 연산자 (Operator)]]
4. [[#4. 조건문 (Conditional)]]
5. [[#5. 반복문 (Loop)]]

---

## 1. 변수 (Variable)

> [!info] 변수란? 데이터를 저장하는 **이름이 붙은 공간**. 파이썬은 변수 선언 시 자료형을 따로 명시하지 않음 (동적 타이핑)

### 변수 선언 및 할당

```python
name = "홍길동"      # 문자열 저장
age = 20             # 정수 저장
height = 175.5       # 실수 저장
```

### 변수 이름 규칙

|규칙|설명|예시|
|---|---|---|
|영문자/숫자/언더스코어(_) 사용|숫자로 시작 불가|`score1` (O), `1score` (X)|
|대소문자 구분|`Age`와 `age`는 다른 변수|—|
|예약어 사용 불가|`if`, `for`, `while` 등|—|
|관례상 소문자+언더스코어|snake_case 권장|`my_variable`|

### 동시 할당 (다중 할당)

```python
a, b, c = 1, 2, 3
x = y = 10          # x와 y 모두 10
```

> [!tip] 변수 값 교환 (swap)
> 
> ```python
> a, b = 1, 2
> a, b = b, a   # 임시변수 없이 swap 가능
> print(a, b)   # 2 1
> ```

---

## 2. 자료형 (Data Type)

### 2-1. 기본 자료형 개요

|자료형|키워드|예시|
|---|---|---|
|정수형|`int`|`10`, `-5`, `0`|
|실수형|`float`|`3.14`, `-0.5`|
|문자열|`str`|`"hello"`, `'파이썬'`|
|불리언|`bool`|`True`, `False`|
|리스트|`list`|`[1, 2, 3]`|
|튜플|`tuple`|`(1, 2, 3)`|
|딕셔너리|`dict`|`{"key": "value"}`|

### 2-2. 자료형 확인: `type()`

```python
print(type(10))        # <class 'int'>
print(type(3.14))      # <class 'float'>
print(type("hi"))      # <class 'str'>
print(type(True))      # <class 'bool'>
```

### 2-3. 숫자형 (int, float)

```python
a = 10        # int
b = 3.5       # float
c = 10 / 3    # 나눗셈 결과는 항상 float → 3.333...
```

> [!note] 정수형과 실수형 자동 변환 정수와 실수를 연산하면 결과는 자동으로 **float**가 됨
> 
> ```python
> result = 5 + 2.0   # 7.0 (float)
> ```

### 2-4. 문자열 (str)

```python
s1 = '작은따옴표'
s2 = "큰따옴표"
s3 = """여러 줄
문자열"""
```

**문자열 연산**

| 연산      | 의미         | 예시             | 결과         |
| ------- | ---------- | -------------- | ---------- |
| `+`     | 연결(concat) | `"안" + "녕"`    | `"안녕"`     |
| `*`     | 반복         | `"ab" * 3`     | `"ababab"` |
| `len()` | 길이         | `len("hello")` | `5`        |
| `[]`    | 인덱싱        | `"hello"[0]`   | `'h'`      |
| `[:]`   | 슬라이싱       | `"hello"[1:3]` | `'el'`     |

**자주 쓰는 문자열 메서드**

```python
s = "Hello World"
s.upper()       # 'HELLO WORLD'
s.lower()       # 'hello world'
s.replace("World", "Python")  # 'Hello Python'
s.split(" ")    # ['Hello', 'World']
s.strip()       # 양쪽 공백 제거
```

**f-string (문자열 포매팅)**

```python
name = "철수"
age = 20
print(f"제 이름은 {name}이고, 나이는 {age}살입니다.")
# 제 이름은 철수이고, 나이는 20살입니다.
```

### 2-5. 불리언 (bool)

```python
is_student = True
is_adult = False
```

- `True`, `False` 값을 가짐 (첫 글자 **대문자**)
- 조건문, 반복문의 판단 기준으로 사용

> [!tip] 파이썬에서 False로 취급되는 값 `0`, `0.0`, `""`(빈 문자열), `[]`(빈 리스트), `None` → 모두 `False`로 취급됨

### 2-6. 형 변환 (Type Casting)

| 함수        | 기능       | 예시                       |
| --------- | -------- | ------------------------ |
| `int()`   | 정수로 변환   | `int("10")` → `10`       |
| `float()` | 실수로 변환   | `float("3.14")` → `3.14` |
| `str()`   | 문자열로 변환  | `str(10)` → `"10"`       |
| `bool()`  | 불리언으로 변환 | `bool(0)` → `False`      |

```python
age_str = "20"
age_int = int(age_str)   # "20" → 20
print(age_int + 1)       # 21
```

---

## 3. 연산자 (Operator)

### 3-1. 산술 연산자

| 연산자  | 의미         | 예시       | 결과         |
| ---- | ---------- | -------- | ---------- |
| `+`  | 덧셈         | `7 + 3`  | `10`       |
| `-`  | 뺄셈         | `7 - 3`  | `4`        |
| `*`  | 곱셈         | `7 * 3`  | `21`       |
| `/`  | 나눗셈 (실수)   | `7 / 3`  | `2.333...` |
| `//` | 몫 (정수 나눗셈) | `7 // 3` | `2`        |
| `%`  | 나머지        | `7 % 3`  | `1`        |
| `**` | 거듭제곱       | `2 ** 3` | `8`        |

### 3-2. 비교 연산자

| 연산자  | 의미     | 예시       | 결과      |
| ---- | ------ | -------- | ------- |
| `==` | 같다     | `5 == 5` | `True`  |
| `!=` | 다르다    | `5 != 3` | `True`  |
| `>`  | 크다     | `5 > 3`  | `True`  |
| `<`  | 작다     | `5 < 3`  | `False` |
| `>=` | 크거나 같다 | `5 >= 5` | `True`  |
| `<=` | 작거나 같다 | `5 <= 4` | `False` |

> [!warning] `=` vs `==` `=` 는 **대입(할당)**, `==` 는 **비교(같은지 확인)** → 헷갈리지 않도록 주의!

### 3-3. 논리 연산자

|연산자|의미|예시|결과|
|---|---|---|---|
|`and`|둘 다 참이면 참|`True and False`|`False`|
|`or`|하나라도 참이면 참|`True or False`|`True`|
|`not`|부정|`not True`|`False`|

```python
age = 20
has_id = True

if age >= 19 and has_id:
    print("입장 가능")
```

### 3-4. 대입 연산자 (복합 대입)

|연산자|의미|예시 (동일 표현)|
|---|---|---|
|`+=`|더하고 대입|`x += 1` → `x = x + 1`|
|`-=`|빼고 대입|`x -= 1` → `x = x - 1`|
|`*=`|곱하고 대입|`x *= 2` → `x = x * 2`|
|`/=`|나누고 대입|`x /= 2` → `x = x / 2`|
|`//=`|몫 대입|`x //= 2`|
|`%=`|나머지 대입|`x %= 2`|
|`**=`|거듭제곱 대입|`x **= 2`|

### 3-5. 연산자 우선순위 (간단 정리)

```
높음  ()  →  **  →  *, /, //, %  →  +, -  →  비교연산자  →  not  →  and  →  or  낮음
```

---

## 4. 조건문 (Conditional)

### 4-1. 기본 if 문

```python
score = 85

if score >= 90:
    print("A학점")
elif score >= 80:
    print("B학점")
elif score >= 70:
    print("C학점")
else:
    print("F학점")

# 출력: B학점
```

> [!warning] 들여쓰기(Indentation) 필수 파이썬은 `{}` 대신 **들여쓰기로 코드 블록**을 구분함. 보통 **스페이스 4칸**을 사용

### 4-2. if 문 구조 정리

| 키워드    | 의미              | 필수 여부       |
| ------ | --------------- | ----------- |
| `if`   | 조건 검사 시작        | 필수          |
| `elif` | 추가 조건 (else if) | 선택, 여러 개 가능 |
| `else` | 모든 조건 불일치 시     | 선택, 1개만     |

### 4-3. 중첩 조건문

```python
age = 25
has_ticket = True

if age >= 19:
    if has_ticket:
        print("입장 가능")
    else:
        print("티켓이 필요합니다")
else:
    print("미성년자는 입장 불가")
```

### 4-4. 한 줄 조건문 (삼항 연산자)

```python
age = 20
result = "성인" if age >= 19 else "미성년자"
print(result)   # 성인
```

---

## 5. 반복문 (Loop)

### 5-1. for문

```python
for i in range(5):
    print(i)
# 0 1 2 3 4 출력
```

**`range()` 함수 사용법**

|표현|의미|예시 결과|
|---|---|---|
|`range(5)`|0~4|`0,1,2,3,4`|
|`range(1, 5)`|1~4|`1,2,3,4`|
|`range(0, 10, 2)`|0~9, 2씩 증가|`0,2,4,6,8`|
|`range(10, 0, -1)`|10~1, 1씩 감소|`10,9,...,1`|

**리스트/문자열 반복**

```python
fruits = ["사과", "바나나", "포도"]
for fruit in fruits:
    print(fruit)

for ch in "Python":
    print(ch)   # 한 글자씩 출력
```

### 5-2. while문

```python
count = 0
while count < 5:
    print(count)
    count += 1
# 0 1 2 3 4 출력
```

> [!danger] 무한 루프 주의 조건을 변화시키는 코드(`count += 1` 등)를 빠뜨리면 **무한 루프**에 빠짐

### 5-3. 반복 제어문

|키워드|기능|
|---|---|
|`break`|반복문을 즉시 **종료**|
|`continue`|현재 반복을 건너뛰고 **다음 반복**으로|
|`pass`|아무 동작 없이 **그냥 통과** (자리만 채움)|

```python
# break 예시
for i in range(10):
    if i == 5:
        break
    print(i)
# 0 1 2 3 4 출력 (5에서 멈춤)

# continue 예시
for i in range(5):
    if i == 2:
        continue
    print(i)
# 0 1 3 4 출력 (2는 건너뜀)
```

### 5-4. 중첩 반복문 (이중 for문)

```python
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} x {j} = {i*j}")
```

> [!example] 구구단 출력 예시
> 
> ```python
> for i in range(2, 10):
>     for j in range(1, 10):
>         print(f"{i} x {j} = {i*j}")
>     print()  # 단마다 줄바꿈
> ```

### 5-5. for-else / while-else

> [!tip] else는 반복문이 break 없이 정상 종료될 때만 실행
> 
> ```python
> for i in range(5):
>     if i == 10:
>         break
> else:
>     print("break 없이 끝까지 실행됨")
> ```

---

## 🧠 핵심 요약 표

|개념|핵심 키워드|
|---|---|
|변수|동적 타이핑, snake_case|
|자료형|int, float, str, bool, type()|
|연산자|산술, 비교, 논리, 복합대입|
|조건문|if / elif / else, 들여쓰기|
|반복문|for + range(), while, break/continue|

---

## 🔗 관련 노트

- **자료구조**  리스트, 튜플, 딕셔너리, 세트
- **함수** 함수 정의, 매개변수, 리턴값
- **모듈과 패키지**