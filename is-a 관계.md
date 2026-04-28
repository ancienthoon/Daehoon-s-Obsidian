# C++ Is-A 관계

---

## Is-A란?

> **"A는 B의 일종이다"** 를 코드로 표현한 것 → **상속**으로 구현

```
"개는 동물이다"    Dog is-a Animal     → class Dog : public Animal
"원은 도형이다"    Circle is-a Shape   → class Circle : public Shape
"학생은 사람이다"  Student is-a Person → class Student : public Person
```

> 💡 **Has-A와 비교:**
> 
> - Is-A → "A는 B이다" → 상속 (inheritance)
> - Has-A → "A는 B를 갖는다" → 포함 (composition)

---

## Is-A 판단 기준

```
✅ Is-A 맞는 경우       → 상속 사용
   개는 동물이다
   정사각형은 사각형이다

❌ Is-A 틀린 경우       → 포함(Has-A) 사용
   자동차는 엔진이다 (X) → 자동차는 엔진을 갖는다 (O)
   사람은 이름이다  (X) → 사람은 이름을 갖는다   (O)
```

---

## 기본 구조

```cpp
// 부모 클래스 (Base)
class Animal {
protected:
    string name;
public:
    Animal(string n) : name(n) { }
    void eat()   { cout << name << " 먹는다" << endl; }
    void sleep() { cout << name << " 잔다"   << endl; }
};

// 자식 클래스 (Derived) — Animal is-a 관계
class Dog : public Animal {
public:
    Dog(string n) : Animal(n) { }   // 부모 생성자 호출
    void bark() { cout << name << " 멍멍" << endl; }
};

int main() {
    Dog d("멍이");
    d.eat();    // ✅ 부모 메서드 사용 가능
    d.sleep();  // ✅
    d.bark();   // ✅ 자식 메서드
}
```

---

## 업캐스팅 / 다운캐스팅

### 업캐스팅 (자식 → 부모 타입)

> Is-A 관계이기 때문에 자식은 부모 타입으로 자동 변환 가능

```cpp
Dog d("멍이");

Animal* a = &d;       // ✅ 업캐스팅 — 자동 변환
Animal& ar = d;       // ✅ 참조도 가능

a->eat();             // 부모 함수 호출
// a->bark();         // ❌ Animal 포인터로는 bark() 접근 불가
```

### 다운캐스팅 (부모 → 자식 타입)

> 명시적 캐스팅 필요, 잘못 쓰면 위험

```cpp
Animal* a = new Dog("멍이");

Dog* d = static_cast<Dog*>(a);   // 다운캐스팅
d->bark();                        // ✅

// 안전하게 하려면 dynamic_cast 사용
Dog* d2 = dynamic_cast<Dog*>(a);
if (d2) d2->bark();               // null 체크 후 사용
```

---

## 다형성 (Polymorphism)

> Is-A 관계 + virtual → **같은 코드로 다른 동작**

```cpp
class Animal {
public:
    virtual void sound() { cout << "..." << endl; }
};

class Dog : public Animal {
public:
    void sound() override { cout << "멍멍" << endl; }
};

class Cat : public Animal {
public:
    void sound() override { cout << "야옹" << endl; }
};

int main() {
    Animal* animals[2];
    animals[0] = new Dog();
    animals[1] = new Cat();

    for (int i = 0; i < 2; i++)
        animals[i]->sound();
    // 멍멍
    // 야옹
}
```

```
virtual 없으면 → 포인터 타입(Animal) 기준 → "..." 출력
virtual 있으면 → 실제 객체 타입 기준    → 각자 다른 출력 ✅
```

---

## Is-A 설계 예시

```cpp
class Shape {                          // 부모
public:
    virtual double area() = 0;         // 순수 가상함수
    virtual void print() {
        cout << "넓이: " << area() << endl;
    }
};

class Circle : public Shape {          // Circle is-a Shape
    double r;
public:
    Circle(double r) : r(r) { }
    double area() override { return 3.14 * r * r; }
};

class Rect : public Shape {            // Rect is-a Shape
    double w, h;
public:
    Rect(double w, double h) : w(w), h(h) { }
    double area() override { return w * h; }
};

int main() {
    Shape* shapes[2];
    shapes[0] = new Circle(5);
    shapes[1] = new Rect(4, 3);

    for (auto s : shapes)
        s->print();                    // 각자 다른 area() 호출
}
```

---

## Is-A vs Has-A 최종 비교

||Is-A|Has-A|
|---|---|---|
|의미|A는 B의 일종|A는 B를 가짐|
|구현|상속|멤버변수|
|문법|`class A : public B`|`class A { B b; }`|
|관계|일반화 / 특수화|구성|
|예시|개는 동물이다|자동차는 엔진을 갖는다|

---

## 핵심 정리

```
Is-A = 상속으로 구현

체크리스트:
✅ "A는 B이다" 문장이 자연스러운가?
✅ 자식이 부모의 모든 기능을 그대로 써도 되는가?
→ 둘 다 YES → Is-A → 상속 사용

포인터로 자식 객체 다룰 때:
- 업캐스팅 (자식→부모) → 자동 변환 ✅
- 다운캐스팅 (부모→자식) → 명시적 캐스팅 필요
- virtual 함수 → 실제 객체 기준 호출 (다형성)
- 가상 소멸자 → 부모 포인터로 delete 시 필수
```

---

_Tags: #Cpp #IsA #상속 #다형성 #업캐스팅 #virtual #객체지향_