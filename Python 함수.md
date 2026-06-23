# 파이썬 — 함수 (Function)

#파이썬 #함수 #def #lambda

---

## 📌 목차

1. [[#1. 함수란?]]
2. [[#2. 함수 정의와 호출]]
3. [[#3. 매개변수 (Parameter)]]
4. [[#4. 반환값 (Return Value)]]
5. [[#5. 변수의 범위 (Scope)]]
6. [[#6. 람다 함수 (Lambda)]]
7. [[#7. 자주 실수하는 포인트]]

---

## 1. 함수란?

> [!info] 핵심 개념 **특정 작업을 수행하는 코드 묶음**에 이름을 붙여 놓은 것. 필요할 때마다 이름을 불러서 재사용할 수 있음.

### 함수를 쓰는 이유

|이유|설명|
|---|---|
|**재사용**|같은 코드를 반복 작성하지 않아도 됨|
|**가독성**|코드가 무슨 일을 하는지 이름만 봐도 알 수 있음|
|**유지보수**|수정할 때 함수 안만 고치면 됨|

> [!tip] C++ 함수와 비교 개념은 완전히 동일. 차이점만 기억하면 됨.
> 
> ||C++|Python|
> |---|---|---|
> |반환 타입 명시|필수 (`int`, `void` 등)|❌ 없음|
> |매개변수 타입|필수|❌ 없음|
> |선언부(헤더)|필요한 경우 있음|❌ 없음|
> 
> ```cpp
> // C++
> int add(int a, int b) {
>     return a + b;
> }
> ```
> 
> ```python
> # Python
> def add(a, b):
>     return a + b
> ```

---

## 2. 함수 정의와 호출

### 기본 구조

```python
def 함수이름(매개변수):
    """독스트링: 함수 설명 (선택)"""
    실행할 코드
    return 반환값
```

### 예시

```python
# 함수 정의
def greet(name):
    """이름을 받아 인사말을 출력하는 함수"""
    print(f"안녕하세요, {name}님!")

# 함수 호출
greet("홍길동")   # 안녕하세요, 홍길동님!
greet("철수")     # 안녕하세요, 철수님!
```

### 함수의 4가지 형태

|형태|설명|예시|
|---|---|---|
|입력 ❌ 출력 ❌|매개변수도 반환값도 없음|단순 출력 함수|
|입력 ✅ 출력 ❌|값을 받아 처리만 함|출력 함수|
|입력 ❌ 출력 ✅|항상 같은 값을 반환|상수 반환 함수|
|입력 ✅ 출력 ✅|값을 받아 계산 후 반환|일반적인 계산 함수|

```python
# 입력 ❌ 출력 ❌
def say_hello():
    print("Hello!")

# 입력 ✅ 출력 ❌
def print_double(x):
    print(x * 2)

# 입력 ❌ 출력 ✅
def get_pi():
    return 3.14159

# 입력 ✅ 출력 ✅
def add(a, b):
    return a + b
```

---

## 3. 매개변수 (Parameter)

> [!info] 용어 정리
> 
> - **매개변수(Parameter)**: 함수를 **정의**할 때 쓰는 변수 이름 → `def add(a, b)`의 `a`, `b`
> - **인수(Argument)**: 함수를 **호출**할 때 실제로 전달하는 값 → `add(3, 5)`의 `3`, `5`

### 3-1. 기본값 매개변수 (Default Parameter)

매개변수에 기본값을 미리 설정해두는 것. 호출 시 해당 인수를 생략하면 기본값이 사용됨.

```python
def introduce(name, age=20, city="부산"):
    print(f"이름: {name}, 나이: {age}, 도시: {city}")

introduce("홍길동")              # 이름: 홍길동, 나이: 20, 도시: 부산
introduce("철수", 25)            # 이름: 철수, 나이: 25, 도시: 부산
introduce("영희", 22, "서울")    # 이름: 영희, 나이: 22, 도시: 서울
```

> [!warning] 기본값 매개변수는 반드시 뒤에 위치 기본값이 있는 매개변수는 기본값이 없는 매개변수보다 **뒤에** 와야 함
> 
> ```python
> def func(a=1, b):   # ❌ SyntaxError
>     pass
> 
> def func(a, b=1):   # ✅ 올바른 순서
>     pass
> ```

### 3-2. 키워드 인수 (Keyword Argument)

호출할 때 **매개변수 이름을 직접 지정**해서 전달하는 방식. 순서에 상관없이 원하는 매개변수에만 값을 넣을 수 있음.

```python
def introduce(name, age, city):
    print(f"이름: {name}, 나이: {age}, 도시: {city}")

# 순서 바꿔서 키워드로 전달
introduce(age=22, city="서울", name="영희")
# 이름: 영희, 나이: 22, 도시: 서울
```

### 3-3. 가변 인수 `*args`

인수의 개수가 정해지지 않았을 때 사용. 전달된 인수들을 **튜플**로 묶어서 받음.

```python
def total(*args):
    print(type(args))    # <class 'tuple'>
    return sum(args)

print(total(1, 2, 3))          # 6
print(total(10, 20, 30, 40))   # 100
print(total(5))                # 5
```

> [!tip] `*args` 이름은 관례일 뿐 `*numbers`, `*values` 등 원하는 이름 사용 가능. `*` 기호가 핵심.
> 
> ```python
> def add_all(*numbers):
>     return sum(numbers)
> ```

### 3-4. 키워드 가변 인수 `**kwargs`

**키워드 형태**로 개수 제한 없이 인수를 받을 때 사용. 전달된 키워드 인수들을 **딕셔너리**로 묶어서 받음.

```python
def print_info(**kwargs):
    print(type(kwargs))   # <class 'dict'>
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="홍길동", age=20, city="부산")
# name: 홍길동
# age: 20
# city: 부산
```

### 3-5. 매개변수 순서 규칙

여러 종류의 매개변수를 함께 쓸 때 반드시 지켜야 할 순서:

```
def 함수(일반, 기본값, *args, **kwargs)
```

```python
def func(a, b=10, *args, **kwargs):
    print(a, b, args, kwargs)

func(1, 2, 3, 4, x=5, y=6)
# a=1, b=2, args=(3,4), kwargs={'x':5,'y':6}
```

### 매개변수 종류 한눈에 보기

|종류|문법|전달 방식|묶이는 형태|
|---|---|---|---|
|일반 매개변수|`def f(a)`|순서대로|단일 값|
|기본값 매개변수|`def f(a=1)`|생략 가능|단일 값|
|키워드 인수|`f(a=1)` 호출 시|이름으로|단일 값|
|가변 인수|`def f(*args)`|여러 개|**튜플**|
|키워드 가변 인수|`def f(**kwargs)`|키워드 여러 개|**딕셔너리**|

---

## 4. 반환값 (Return Value)

### 기본 반환

```python
def square(x):
    return x ** 2

result = square(4)
print(result)   # 16
```

### 여러 값 반환 (튜플로 묶여서 반환됨)

```python
def min_max(lst):
    return min(lst), max(lst)   # 내부적으로 튜플 (min, max)

lo, hi = min_max([3, 1, 4, 1, 5, 9])
print(lo, hi)   # 1 9

# 튜플째로 받기
result = min_max([3, 1, 4])
print(result)   # (1, 4)
```

> [!info] `return` 없으면 `None` 반환 파이썬의 모든 함수는 반드시 무언가를 반환함. `return` 문이 없거나 `return`만 쓰면 자동으로 `None` 반환.
> 
> ```python
> def nothing():
>     print("아무것도 반환 안 함")
> 
> result = nothing()
> print(result)   # None
> ```

### `return`의 2가지 역할

```python
def check_positive(n):
    if n <= 0:
        return          # ① 함수 즉시 종료 (None 반환)
    return n * 2        # ② 값을 반환하면서 종료
```

---

## 5. 변수의 범위 (Scope)

> [!info] 핵심 개념 변수가 **어디서 만들어졌느냐**에 따라 접근 가능한 범위가 달라짐.

### 지역 변수 vs 전역 변수

|구분|위치|접근 범위|
|---|---|---|
|**지역 변수 (Local)**|함수 **안**에서 선언|해당 함수 안에서만|
|**전역 변수 (Global)**|함수 **밖**에서 선언|파일 전체|

```python
x = 10   # 전역 변수

def func():
    y = 20   # 지역 변수
    print(x)  # ✅ 전역 변수 읽기 가능
    print(y)  # ✅ 지역 변수 읽기 가능

func()
print(x)   # ✅ 전역 변수 읽기 가능
print(y)   # ❌ NameError! 지역 변수는 함수 밖에서 접근 불가
```

### 함수 안에서 전역 변수 수정: `global`

```python
count = 0   # 전역 변수

def increment():
    global count       # "이 count는 전역 변수를 쓰겠다" 선언
    count += 1

increment()
increment()
print(count)   # 2
```

> [!warning] `global` 남용은 위험 `global`을 많이 쓰면 코드가 복잡해지고 버그 찾기 어려워짐. 가능하면 **함수의 매개변수와 반환값으로 해결**하는 것이 좋은 습관.
> 
> ```python
> # ❌ global 남용
> total = 0
> def add(x):
>     global total
>     total += x
> 
> # ✅ 권장 방식
> def add(total, x):
>     return total + x
> 
> total = 0
> total = add(total, 5)
> ```

### LEGB 규칙 (변수 탐색 순서)

파이썬이 변수를 찾을 때 탐색하는 순서:

```
L (Local)    → 현재 함수 안
E (Enclosing)→ 바깥쪽 함수 (중첩 함수일 때)
G (Global)   → 파일 전체 범위
B (Built-in) → 파이썬 내장 함수 (print, len 등)
```

```python
x = "전역"          # Global

def outer():
    x = "외부함수"   # Enclosing

    def inner():
        x = "내부함수"  # Local
        print(x)        # Local 우선 탐색 → "내부함수"

    inner()
    print(x)            # Enclosing 탐색 → "외부함수"

outer()
print(x)               # Global 탐색 → "전역"
```

---

## 6. 람다 함수 (Lambda)

> [!info] 핵심 개념 이름 없이 **한 줄**로 간단하게 만드는 함수. 단순한 연산을 짧게 표현할 때 사용.

### 기본 문법

```python
# def 방식
def add(a, b):
    return a + b

# lambda 방식 (완전히 동일한 동작)
add = lambda a, b: a + b

print(add(3, 5))   # 8
```

```
lambda 매개변수 : 반환값
```

### 람다의 진가 — 다른 함수의 인수로 바로 넣을 때

```python
nums = [3, 1, 4, 1, 5, 9, 2]

# sorted의 key 인수로 람다 사용
sorted_nums = sorted(nums)
print(sorted_nums)   # [1, 1, 2, 3, 4, 5, 9]

# 절댓값 기준으로 정렬
nums2 = [-3, 1, -4, 2]
sorted_abs = sorted(nums2, key=lambda x: abs(x))
print(sorted_abs)    # [1, 2, -3, -4]

# 문자열 리스트를 길이 기준으로 정렬
words = ["banana", "apple", "kiwi", "cherry"]
sorted_words = sorted(words, key=lambda x: len(x))
print(sorted_words)  # ['kiwi', 'apple', 'banana', 'cherry']
```

### `def` vs `lambda` 비교

||`def`|`lambda`|
|---|---|---|
|이름|있음|없음 (익명)|
|줄 수|여러 줄 가능|**1줄만**|
|복잡한 로직|✅ 가능|❌ 단순 표현만|
|재사용|✅ 이름으로 호출|보통 1회성으로 사용|
|사용 시점|일반적 함수 정의|짧은 함수가 인수로 필요할 때|

> [!tip] 람다는 짧을 때만 쓰기 복잡한 로직이라면 람다 대신 `def`로 정의하는 게 가독성에 훨씬 좋음.
> 
> ```python
> # ❌ 람다가 오히려 읽기 어려운 경우
> f = lambda x: x**2 if x > 0 else -x**2 if x < 0 else 0
> 
> # ✅ def로 명확하게
> def signed_square(x):
>     if x > 0:
>         return x ** 2
>     elif x < 0:
>         return -(x ** 2)
>     return 0
> ```

---

## 7. 자주 실수하는 포인트

> [!warning] 함수는 호출 전에 정의되어야 함
> 
> ```python
> say_hello()      # ❌ NameError! 아직 정의 안 됨
> 
> def say_hello():
>     print("Hello!")
> 
> say_hello()      # ✅ 정의 후 호출
> ```

> [!warning] 기본값에 리스트/딕셔너리 사용 금지 기본값이 **가변 객체(리스트, 딕셔너리)** 이면 함수 호출마다 공유되는 버그 발생
> 
> ```python
> # ❌ 위험한 코드
> def append_item(item, lst=[]):
>     lst.append(item)
>     return lst
> 
> print(append_item(1))   # [1]
> print(append_item(2))   # [1, 2]  ← 초기화가 안 됨!
> 
> # ✅ 올바른 방법 — 기본값을 None으로 설정
> def append_item(item, lst=None):
>     if lst is None:
>         lst = []
>     lst.append(item)
>     return lst
> 
> print(append_item(1))   # [1]
> print(append_item(2))   # [2]  ← 정상
> ```

> [!warning] `return`과 `print`를 혼동하지 말 것
> 
> ```python
> def add(a, b):
>     print(a + b)     # 화면에 출력만 하고 반환값은 None
> 
> result = add(3, 5)   # 8이 출력되지만
> print(result)        # None  ← 반환값은 None!
> 
> # 반환값이 필요하면 return 사용
> def add(a, b):
>     return a + b
> 
> result = add(3, 5)
> print(result)        # 8
> ```

> [!warning] 지역 변수는 함수 밖에서 사용 불가
> 
> ```python
> def calc():
>     result = 100
> 
> calc()
> print(result)   # ❌ NameError! result는 함수 안에서만 존재
> 
> # 해결: return으로 꺼내기
> def calc():
>     result = 100
>     return result
> 
> result = calc()
> print(result)   # ✅ 100
> ```

---

## 🧠 전체 핵심 요약

```python
# ① 기본 함수
def greet(name):
    return f"안녕, {name}!"

# ② 기본값 매개변수
def power(base, exp=2):
    return base ** exp

# ③ 가변 인수
def total(*args):
    return sum(args)

# ④ 키워드 가변 인수
def show(**kwargs):
    for k, v in kwargs.items():
        print(f"{k}: {v}")

# ⑤ 여러 값 반환 (튜플)
def range_info(lst):
    return min(lst), max(lst)

# ⑥ 람다
square = lambda x: x ** 2
sorted_list = sorted([3,1,2], key=lambda x: -x)
```

---

## 🔗 관련 노트

- [[Python 기초 (변수, 자료형, 연산자, 조건문, 반복문)]] — 변수, 자료형, 조건문, 반복문
- [[리스트]] — 함수와 함께 자주 사용
- [[딕셔너리]] — `**kwargs`와 밀접한 관련
- [[튜플]] — 여러 값 반환 시 튜플로 묶임