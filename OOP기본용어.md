# 📘 C++ 9장 — 다형성 (Polymorphism)

---

## 🔖 목차

1. [[#다형성이란]]
2. [[#객체 포인터의 형변환]]
3. [[#가상 함수 virtual function]]
4. [[#동적 바인딩 vs 정적 바인딩]]
5. [[#가상 소멸자 virtual destructor]]
6. [[#순수 가상 함수와 추상 클래스]]
7. [[#추상 클래스를 인터페이스로]]
8. [[#핵심 정리]]

---

## 📌 다형성이란

> **개념**: 객체들의 타입이 달라도 **똑같은 메시지(함수 호출)**에 대해 각자 다르게 동작하는 것

```
speak() 호출
  ├── Dog 객체  → "멍멍"
  └── Cat 객체  → "야옹"
```

- 하나의 코드로 다양한 타입의 객체를 처리할 수 있음
- 객체지향 프로그래밍의 핵심 기술 중 하나

---

## 📌 객체 포인터의 형변환

### 상향 형변환 (Upcasting)

> 자식 클래스 타입 → 부모 클래스 타입으로 변환 (**자식이 부모로 위장**)

```cpp
Dog a;
Animal *pa = &a;       // ✅ OK — 부모 포인터로 자식 객체 가리킴
pa = new Dog();        // ✅ OK

Animal a;
Dog *ps = &a;          // ❌ Error — 자식 포인터는 부모 객체 가리킬 수 없음
```

### 상향 형변환 후 접근 제한

```cpp
Shape *ps = new Rectangle();   // 상향 형변환
ps->setOrigin(10, 10);         // ✅ 부모(Shape) 멤버함수 — 가능
ps->setWidth(100);             // ❌ 자식(Rectangle) 멤버함수 — 불가
```

> **이유**: `ps`는 Shape 포인터로 선언됐으므로 컴파일러는 Shape로만 간주

### 하향 형변환 (Downcasting)

> 부모 포인터 → 자식 포인터로 변환하여 자식 멤버 접근

```cpp
Shape *ps = new Rectangle();            // 상향 형변환

// 방법 1 — 새 포인터 변수 사용
Rectangle *pr = (Rectangle *) ps;
pr->setWidth(100);                       // ✅ 자식 함수 사용 가능

// 방법 2 — 인라인 형변환
((Rectangle *) ps)->setWidth(100);      // ✅ 동일한 결과
```

### 형변환 규칙 요약

|방향|가능 여부|설명|
|---|---|---|
|자식 → 부모 (상향)|✅ 가능|자동 형변환|
|부모 → 자식 (하향)|⚠️ 명시적 캐스팅 필요|`(자식타입 *)` 로 변환|
|자식 포인터 → 부모 객체|❌ 불가|컴파일 오류|

### 부모 타입 매개변수의 장점

```cpp
// 이렇게 하면 Shape의 모든 자식(Circle, Rectangle, ...)을 한 함수로 받을 수 있음
void move(Shape& s, int sx, int sy) {
    s.setOrigin(sx, sy);
}

Rectangle r;  move(r, 0, 0);   // ✅
Circle c;     move(c, 10, 10); // ✅
```

---

## 📌 가상 함수 virtual function

### 문제 상황 — 가상 함수 없을 때

```cpp
Shape *ps = new Rectangle();
ps->draw();   // → Shape의 draw() 호출 ← 원하는 게 아님!
```

> 부모 포인터이므로 항상 **부모의 함수**가 호출됨

### 해결 — virtual 키워드 사용

```cpp
class Shape {
public:
    virtual void draw() {         // ← virtual 선언
        cout << "Shape Draw" << endl;
    }
};

class Rectangle : public Shape {
public:
    void draw() {                 // 재정의 (overriding)
        cout << "Rectangle Draw" << endl;
    }
};

class Circle : public Shape {
public:
    void draw() {
        cout << "Circle Draw" << endl;
    }
};
```

```cpp
Shape *ps = new Rectangle();
ps->draw();   // → "Rectangle Draw" ✅ 실제 객체 기준으로 호출

Shape *ps1 = new Circle();
ps1->draw();  // → "Circle Draw"   ✅
```

### 가상 함수 동작 원리

```
부모 포인터로 가상 함수 호출
  → 컴파일러: "이 포인터가 실제로 무엇을 가리키는가?" 실행 시간에 확인
  → 실제 객체(Rectangle)의 draw() 호출
```

### 가상 함수 규칙

- 부모에서 `virtual` 선언하면 **자식의 동일 원형 함수도 자동으로 가상함수**
- 자식 클래스의 재정의 함수는 `virtual` 생략 가능 (붙여도 됨)
- **함수 원형(이름, 반환형, 매개변수)이 동일**해야 재정의(overriding) 성립

### 배열로 다형성 활용

```cpp
Shape *arrayOfShapes[3];
arrayOfShapes[0] = new Rectangle();
arrayOfShapes[1] = new Triangle();
arrayOfShapes[2] = new Circle();

for (int i = 0; i < 3; i++)
    arrayOfShapes[i]->draw();
// Rectangle Draw
// Triangle Draw
// Circle Draw
```

> **다형성 장점**: 새로운 도형 클래스가 추가되어도 **main()의 루프 코드는 수정 불필요**

### 가상 함수 사용 전후 비교

```cpp
// ❌ 가상 함수 없을 때 — 변수를 여러 개 써야 함
Dog* p1 = new Dog();   p1->speak();
Cat* p2 = new Cat();   p2->speak();

// ✅ 가상 함수 사용 — 변수 1개로 처리 가능
Animal* a = new Dog();   a->speak();   // 멍멍
a = new Cat();           a->speak();   // 야옹
```

### ⚠️ 가상 함수는 포인터/참조자에서만 작동

```cpp
Dog d;
Animal a1 = d;      // 복사본 — 가상함수 작동 안함 ❌
a1.speak();         // → "Animal speak()" 출력

Animal &a1 = d;     // 참조자 — 가상함수 작동 ✅
a1.speak();         // → "멍멍" 출력
```

---

## 📌 동적 바인딩 vs 정적 바인딩

> **바인딩(Binding)**: 함수 호출 코드와 실제 실행할 함수를 연결하는 것

|구분|결정 시점|속도|대상|
|---|---|---|---|
|**정적 바인딩** (static binding)|컴파일 시간|빠름|일반 함수|
|**동적 바인딩** (dynamic binding)|실행 시간|상대적으로 느림|**가상 함수**|

```
정적: Shape *ps = new Rectangle();  ps->draw();
      → 컴파일 시점에 "Shape::draw() 호출" 으로 고정

동적: Shape *ps = new Rectangle();  ps->draw();  (draw가 virtual)
      → 실행 시점에 ps가 가리키는 실제 객체 확인 → Rectangle::draw() 호출
```

---

## 📌 가상 소멸자 virtual destructor

### 문제 상황

```cpp
Animal *a1 = new Dog();
a1->speak();
delete a1;
// → Animal 소멸자만 호출 ❌
// → Dog 소멸자 호출 안됨 → 메모리 누수!
```

> `a1`이 Animal 포인터이므로 `delete` 시 Animal 소멸자만 호출

### 해결 — 소멸자를 virtual로 선언

```cpp
class Animal {
public:
    virtual ~Animal() { cout << "Animal 소멸자" << endl; }  // virtual
};

class Dog : public Animal {
public:
    virtual ~Dog() { cout << "Dog 소멸자" << endl; }        // virtual
};

Animal *a1 = new Dog();
delete a1;
// 출력:
// Dog 소멸자    ← 자식 먼저
// Animal 소멸자 ← 그 다음 부모 자동 호출
```

### 소멸자 호출 순서

```
delete 부모포인터
  → 가상 소멸자 → 실제 객체 확인
  → 자식 소멸자 호출 (자식 메모리 해제)
  → 부모 소멸자 자동 호출 (부모 메모리 해제)
```

### ⚠️ 핵심 규칙

> **다형성(가상 함수)을 사용하는 클래스라면 소멸자를 반드시 `virtual`로 선언!** 부모 클래스의 소멸자에 `virtual` 붙이면 자식 소멸자는 자동으로 가상함수화

---

## 📌 순수 가상 함수와 추상 클래스

### 순수 가상 함수 (Pure Virtual Function)

> **함수 헤더(원형)만 있고 본체가 없는 가상 함수** 자식 클래스에게 "이 함수는 반드시 네가 직접 구현해라"는 강제 지시

```cpp
virtual 반환형 함수이름(매개변수) = 0;

// 예시
virtual void draw() = 0;
```

### 추상 클래스 (Abstract Class)

> **순수 가상 함수를 하나 이상 포함한 클래스**

```cpp
class Shape {
public:
    virtual void draw() = 0;   // 순수 가상 함수
};

class Line : public Shape {
public:
    void draw() {              // ✅ 자식이 구현
        cout << "직선을 그린다" << endl;
    }
};

class Circle : public Shape {
public:
    void draw() {              // ✅ 자식이 구현
        cout << "원을 그린다" << endl;
    }
};
```

```cpp
Shape s;    // ❌ 오류 — 추상 클래스는 객체 생성 불가
Circle c;   // ✅ OK

Shape *ps = new Line();    // ✅ 포인터는 가능
ps->draw();                // "직선을 그린다"
```

### 가상 함수 vs 순수 가상 함수

|구분|가상 함수|순수 가상 함수|
|---|---|---|
|선언|`virtual void f()`|`virtual void f() = 0`|
|본체|있음 (기본 구현 제공)|없음|
|자식 구현|선택|**강제**|
|객체 생성|가능|불가 (추상 클래스)|

### 언제 쓰는가?

```
가상 함수      → 부모에 기본 구현이 있고, 자식이 필요하면 재정의
순수 가상 함수 → 자식마다 구현이 완전히 달라 부모에 기본 구현이 의미 없을 때
```

---

## 📌 추상 클래스를 인터페이스로

> 다양한 객체들이 **공통된 이름의 함수**로 동작하도록 규격(인터페이스)을 정의할 때 사용

### 홈 네트워킹 예제

```cpp
class RemoteControl {
public:
    virtual void turnON()  = 0;   // 순수 가상함수 — 규격만 정의
    virtual void turnOFF() = 0;
};

class Television : public RemoteControl {
public:
    void turnON()  { /* TV 켜는 실제 코드 */ }
    void turnOFF() { /* TV 끄는 실제 코드 */ }
};

class Refrigerator : public RemoteControl {
public:
    void turnON()  { /* 냉장고 켜는 실제 코드 */ }
    void turnOFF() { /* 냉장고 끄는 실제 코드 */ }
};

// 홈 네트워킹 시스템 — 부모 포인터 하나로 모든 가전 제어
RemoteControl *p;
p = new Television();   p->turnON();    // TV 켜짐
p = new Refrigerator(); p->turnON();    // 냉장고 켜짐
```

---

## 🗂️ 핵심 정리

### 개념 계층 정리

```
다형성 (Polymorphism)
  └── 가상 함수 (virtual)
        ├── 일반 가상 함수    → 부모에 기본 구현 있음
        ├── 순수 가상 함수    → = 0, 본체 없음
        │     └── 추상 클래스 → 객체 생성 불가, 인터페이스 역할
        └── 가상 소멸자       → delete 시 자식 소멸자 정상 호출
```

### 전체 요약표

|개념|키워드|핵심|
|---|---|---|
|상향 형변환|자동|자식 → 부모, 부모 멤버만 사용 가능|
|하향 형변환|`(자식타입*)`|자식 멤버 사용하려면 명시적 캐스팅|
|가상 함수|`virtual`|실제 객체 기준으로 함수 결정 (동적 바인딩)|
|가상 소멸자|`virtual ~`|다형성 사용 시 **반드시** 선언|
|순수 가상 함수|`= 0`|자식에게 구현 강제|
|추상 클래스|순수 가상 포함|객체 생성 불가, 인터페이스로 활용|

### ⚠️ 최다 실수 포인트

|실수 유형|올바른 내용|
|---|---|
|가상 함수를 일반 객체로 사용|`Animal a = dog` → 가상함수 작동 안함, 포인터/참조자 필요|
|가상 소멸자 빠뜨림|다형성 사용 시 부모 소멸자에 `virtual` 필수 → 자식 메모리 누수 방지|
|추상 클래스 객체 생성 시도|`Shape s` → 컴파일 오류, 포인터만 가능|
|자식에서 순수 가상 함수 구현 안 함|자식도 추상 클래스가 됨 → 역시 객체 생성 불가|

---

> 📅 작성일: 2026-05-14 🏫 과목: C++ / 자료구조 📖 단원: 9장 — 다형성, 가상 함수, 순수 가상 함수

#cpp #다형성 #polymorphism #virtual #추상클래스

[[has-a 관계]] [[is-a 관계]] [[상속관계]]

