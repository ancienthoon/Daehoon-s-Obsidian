# C++ 클래스 키워드: this / static / const

## 🗂️ 목차

- [[#this 포인터]]
- [[#static 멤버]]
- [[#const 멤버]]
- [[#셋 비교 총정리]]

---

## this 포인터

### this란?

> **자기 자신 객체를 가리키는 포인터**  
> 모든 멤버 함수 안에서 자동으로 존재함 — 직접 선언할 필요 없음

```cpp
class Car {
    int speed;
public:
    void setSpeed(int speed) {
        this->speed = speed;  // this->speed = 멤버변수
                              //       speed = 매개변수
    }
};
```

> 💡 **비유:** 주민등록증의 "본인"처럼, 어떤 객체든 자기 자신을 가리킬 때 `this` 사용

---

### this가 필요한 상황

#### 상황 1: 매개변수 이름 = 멤버변수 이름이 같을 때

```cpp
class Car {
    int speed;
    string color;
public:
    // speed, color 가 매개변수인지 멤버변수인지 헷갈림
    Car(int speed, string color) {
        this->speed = speed;   // this->speed → 멤버변수
        this->color = color;   // speed       → 매개변수
    }
};
```

#### 상황 2: 자기 자신을 반환할 때 (메서드 체이닝)

```cpp
class Car {
    int speed;
public:
    Car& accelerate(int amount) {
        speed += amount;
        return *this;          // 자기 자신 객체를 반환
    }
};

int main() {
    Car c;
    c.accelerate(10).accelerate(20);  // 연속 호출 가능
}
```

#### 상황 3: 자기 자신을 다른 함수에 넘길 때

```cpp
void someFunction(Car* obj) { ... }

class Car {
public:
    void doSomething() {
        someFunction(this);    // 자기 자신의 주소를 전달
    }
};
```

---

### this 핵심 정리

```
this      → 현재 객체의 포인터 (주소값)
*this     → 현재 객체 자체 (역참조)
this->멤버 → 현재 객체의 멤버에 접근
```

> ⚠️ `static` 멤버 함수 안에서는 `this` 사용 불가  
> (static은 특정 객체에 속하지 않기 때문)

---

## static 멤버

### static이란?

> **모든 객체가 공유하는 멤버**  
> 객체를 만들지 않아도 존재하며, 클래스 전체에 딱 하나만 있음

```
일반 멤버변수: 객체마다 각자 따로 가짐
static 멤버변수: 모든 객체가 하나를 같이 씀
```

> 💡 **비유:** 반 학생 각자의 이름(일반 변수) vs 반 전체 학생 수(static 변수)

---

### static 멤버변수

```cpp
class Car {
public:
    int speed;              // 일반 멤버변수 → 객체마다 따로
    static int count;       // static 멤버변수 → 모든 객체가 공유
    
    Car() { count++; }      // 객체 생성될 때마다 count 증가
    ~Car() { count--; }
};

// ⚠️ 클래스 밖에서 반드시 정의 및 초기화!
int Car::count = 0;

int main() {
    Car c1, c2, c3;
    cout << Car::count << endl;  // 3
    cout << c1.count   << endl;  // 3 (같은 값)
}
```

#### static 멤버변수 규칙

```
선언: 클래스 안에서 static int count;
정의: 클래스 밖에서 int Car::count = 0;  ← 반드시 필요!
접근: Car::count  또는  객체.count
```

> ⚠️ 클래스 밖 정의 안 하면 **링크 에러** 발생!

---

### static 멤버함수

```cpp
class Car {
    static int count;
    int speed;
public:
    Car() { count++; }
    
    static int getCount() {     // static 멤버함수
        return count;           // static 변수만 접근 가능
        // return speed;        // ❌ 에러! 일반 멤버변수 접근 불가
        // this->speed;         // ❌ 에러! this 사용 불가
    }
};
int Car::count = 0;

int main() {
    Car c1, c2;
    
    cout << Car::getCount() << endl;  // 2 (객체 없이 호출 가능)
    cout << c1.getCount()   << endl;  // 2 (객체로도 호출 가능)
}
```

#### static 멤버함수 제약

```
✅ static 멤버변수 접근 가능
✅ 객체 없이 클래스명::함수명() 으로 호출 가능
❌ 일반 멤버변수 접근 불가 (어느 객체의 것인지 모름)
❌ this 포인터 사용 불가
❌ 일반 멤버함수 호출 불가
```

---

### 일반 멤버 vs static 멤버 비교

|구분|일반 멤버|static 멤버|
|---|---|---|
|메모리|객체마다 각자|클래스당 하나|
|생성 시점|객체 생성 시|프로그램 시작 시|
|접근 방법|`객체.멤버`|`클래스::멤버` 또는 `객체.멤버`|
|this 사용|가능|불가|
|용도|객체별 데이터|공유 데이터, 객체 수 세기 등|

---

## const 멤버

### const란?

> **값을 변경하지 않겠다는 약속**  
> 클래스에서는 멤버변수, 멤버함수, 객체 세 곳에 붙일 수 있음

---

### ① const 멤버변수

```cpp
class Car {
    const int MAX_SPEED = 200;  // 선언과 동시에 초기화
    const int id;               // 초기화 목록으로만 초기화 가능
public:
    Car(int i) : id(i) { }      // 반드시 초기화 목록에서 초기화!
};
```

> ⚠️ `const` 멤버변수는 생성자 `{ }` 안에서 `id = i;` 로 대입 불가  
> 반드시 **초기화 목록** `: id(i)` 으로만 초기화 가능

---

### ② const 멤버함수

```cpp
class Car {
    int speed;
    string color;
public:
    // 함수 뒤에 const 붙임
    int getSpeed() const {      // "이 함수는 멤버변수를 바꾸지 않음"
        return speed;           // 읽기만 가능
        // speed = 100;         // ❌ 에러! const 함수에서 값 변경 불가
    }
    
    void setSpeed(int s) {      // 일반 함수는 변경 가능
        speed = s;
    }
};
```

#### const 멤버함수 규칙

```
✅ 멤버변수 읽기 가능
✅ const 멤버함수 호출 가능
❌ 멤버변수 변경 불가
❌ 일반(non-const) 멤버함수 호출 불가
```

---

### ③ const 객체

```cpp
int main() {
    const Car c1(100, "red");   // const 객체 선언
    
    cout << c1.getSpeed();      // ✅ const 함수만 호출 가능
    // c1.setSpeed(200);        // ❌ 에러! 일반 함수 호출 불가
}
```

> 💡 **const 객체 → const 멤버함수만 호출 가능**  
> → getter 함수들은 항상 `const` 붙이는 습관 들이기!

---

### const 위치별 의미 요약

```cpp
const int speed = 100;          // 변수: 값 변경 불가
int getSpeed() const { ... }    // 함수: 멤버변수 변경 불가
const Car c1;                   // 객체: const 함수만 호출 가능
const int* p;                   // 포인터: 가리키는 값 변경 불가
int* const p;                   // 포인터: 포인터 자체 변경 불가
```

---

### getter/setter 패턴에서의 const

```cpp
class Car {
    int speed;
    string color;
public:
    // getter → const 붙이기 (값 안 바꾸니까)
    int    getSpeed() const { return speed; }
    string getColor() const { return color; }
    
    // setter → const 안 붙임 (값 바꾸니까)
    void setSpeed(int s)    { speed = s; }
    void setColor(string c) { color = c; }
};
```

---

## 셋 비교 총정리

### 한눈에 비교

|키워드|붙이는 위치|의미|핵심 제약|
|---|---|---|---|
|`this`|멤버함수 내부|자기 자신 객체 포인터|static 함수에서 사용 불가|
|`static`|멤버변수/함수|모든 객체가 공유|일반 멤버/this 접근 불가|
|`const`|변수/함수/객체|변경 불가|const 함수에서 멤버변수 수정 불가|

---

### 조합 사용 예시

```cpp
class Car {
    int speed;
    static int count;
    const int id;
public:
    Car(int i) : id(i) {
        count++;
        cout << "내 id: " << this->id << endl;
    }
    
    int getSpeed() const {          // const 함수
        return this->speed;         // this 사용
    }
    
    static int getCount() {         // static 함수
        return count;               // this 사용 불가
    }
};
int Car::count = 0;
```

---

### 자주 하는 실수

```cpp
// ❌ static 함수에서 this 사용
static void wrong() {
    this->speed = 0;    // 에러! static에는 this 없음
}

// ❌ const 함수에서 멤버변수 수정
int getSpeed() const {
    speed = 0;          // 에러! const 함수는 읽기만 가능
    return speed;
}

// ❌ const 멤버변수를 생성자 본문에서 초기화
Car(int i) {
    id = i;             // 에러! const 변수는 초기화 목록으로만
}
Car(int i) : id(i) { } // ✅ 올바른 방법

// ❌ const 객체에서 일반 함수 호출
const Car c1;
c1.setSpeed(100);       // 에러! const 객체는 const 함수만 호출 가능
```

---

## 🔑 핵심 암기

```
this   → "나 자신" 포인터, 이름 충돌 해결 / 자신 반환할 때 사용
static → "공유" 멤버, 객체 없이 접근 가능, 클래스 밖에서 정의 필수
const  → "불변" 약속, 함수 뒤에 붙이면 멤버변수 수정 불가

조합 금지:
- static 함수 + this → ❌
- static 함수 + 일반 멤버변수 → ❌
- const 함수 + 멤버변수 수정 → ❌
- const 객체 + 일반 함수 호출 → ❌
```