# C++ Has-A 관계 (포함 관계)

---

## Has-A란?

> **한 클래스가 다른 클래스를 멤버변수로 가지는 관계**

```
"A는 B를 가지고 있다" → A has a B
```

```cpp
class Engine { ... };

class Car {
    Engine engine;   // Car has a Engine
};
```

> 💡 **비유:** 자동차(Car)는 엔진(Engine)을 **가지고** 있다 → 자동차가 엔진인 게 아니라, 자동차 **안에** 엔진이 존재

---

## Is-A vs Has-A 비교

|관계|의미|구현 방식|예시|
|---|---|---|---|
|**Is-A**|A는 B의 일종이다|상속|개는 동물이다|
|**Has-A**|A는 B를 가진다|멤버변수|자동차는 엔진을 가진다|

```
Is-A  → class Dog : public Animal { }      상속
Has-A → class Car { Engine engine; }       포함
```

---

## 기본 코드 구조

```cpp
// 포함되는 클래스
class Engine {
    int cc;
public:
    Engine(int cc) : cc(cc) { }
    void start() { cout << cc << "cc 엔진 시동" << endl; }
};

// 포함하는 클래스
class Car {
    Engine engine;   // Engine 객체를 멤버변수로 가짐
    string color;
public:
    Car(int cc, string c) : engine(cc), color(c) { }
    //                       ↑
    //               초기화 목록으로 Engine 생성자 호출

    void drive() {
        engine.start();   // 멤버 객체의 함수 호출
        cout << color << " 차 출발" << endl;
    }
};

int main() {
    Car car(2000, "red");
    car.drive();
    // 출력:
    // 2000cc 엔진 시동
    // red 차 출발
}
```

---

## 생성자/소멸자 호출 순서

```
객체 생성 순서:
  멤버 객체 생성자 먼저 → 포함하는 클래스 생성자

객체 소멸 순서:
  포함하는 클래스 소멸자 먼저 → 멤버 객체 소멸자
  (생성의 역순)
```

```cpp
class Engine {
public:
    Engine()  { cout << "Engine 생성" << endl; }
    ~Engine() { cout << "Engine 소멸" << endl; }
};

class Car {
    Engine engine;
public:
    Car()  { cout << "Car 생성" << endl; }
    ~Car() { cout << "Car 소멸" << endl; }
};

int main() {
    Car c;
}
// 출력:
// Engine 생성   ← 멤버 먼저
// Car 생성
// Car 소멸      ← 포함하는 것 먼저
// Engine 소멸
```

---

## 멤버 초기화 목록으로 멤버 객체 초기화

```cpp
class Car {
    Engine engine;
    string color;
public:
    // ✅ 초기화 목록에서 Engine 생성자 인자 전달
    Car(int cc, string c) : engine(cc), color(c) { }

    // ❌ 아래처럼 본문에서 대입은 안 됨
    // Car(int cc, string c) {
    //     engine = Engine(cc);  // 이미 기본 생성자로 생성된 후 대입
    // }
};
```

> ⚠️ 멤버 객체는 **반드시 초기화 목록**에서 초기화 본문에서 대입하면 기본 생성자가 먼저 호출된 뒤 덮어씌워지는 비효율 발생

---

## Has-A 포함 방식 2가지

### 방식 1: 값으로 포함 (일반적)

```cpp
class Car {
    Engine engine;   // Engine 객체 자체를 멤버로 가짐
};
```

- Car가 소멸되면 engine도 자동 소멸
- 별도 메모리 관리 불필요

### 방식 2: 포인터로 포함

```cpp
class Car {
    Engine* engine;  // Engine 포인터를 멤버로 가짐
public:
    Car(int cc) { engine = new Engine(cc); }
    ~Car()      { delete engine; }           // 직접 해제 필요
};
```

- 동적 생성/교체 가능
- 소멸자에서 반드시 `delete` 해야 함

---

## 실전 예시

```cpp
class Address {
    string city, street;
public:
    Address(string c, string s) : city(c), street(s) { }
    void print() { cout << city << " " << street << endl; }
};

class Person {
    string name;
    Address addr;    // Person has-a Address
public:
    Person(string n, string c, string s)
        : name(n), addr(c, s) { }

    void print() {
        cout << name << " / ";
        addr.print();
    }
};

int main() {
    Person p("홍길동", "서울", "강남구");
    p.print();
    // 홍길동 / 서울 강남구
}
```

---

## 핵심 정리

```
Has-A = 멤버변수로 다른 클래스 객체를 포함

포인트:
1. 초기화 목록으로 멤버 객체 초기화
2. 생성 순서: 멤버 객체 먼저 → 본 클래스
3. 소멸 순서: 본 클래스 먼저 → 멤버 객체 (역순)
4. 포인터로 포함 시 소멸자에서 delete 필수

Is-A(상속) vs Has-A(포함) 선택 기준:
  "A는 B이다"   → Is-A → 상속
  "A는 B를 갖는다" → Has-A → 멤버변수
```

---

_Tags: #Cpp #HasA #포함관계 #클래스 #객체지향 #상속_