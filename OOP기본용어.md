

---

# 🏗️ C++ 클래스의 기초 (Class Basics)

> [!abstract] **핵심 개념**
> 
> - **클래스(Class):** 객체를 만들기 위한 **설계도(추상화)**. C언어의 `struct`가 진화한 형태.
>     
> - **객체(Object):** 클래스라는 설계도로부터 실제로 만들어진 **실체(인스턴스)**.
>     

---

## 1. 클래스 vs 구조체 vs 객체

|**구분**|**설명**|**비유**|
|---|---|---|
|**클래스 (Class)**|멤버 변수(속성)와 멤버 함수(동작)의 집합|붕어빵 틀|
|**객체 (Object)**|클래스 타입으로 선언된 변수|실제 붕어빵|
|**구조체 (C)**|데이터(변수)만 묶어놓은 형태|단순 포장지|

---

## 2. 클래스의 구성 요소

### ① 멤버 변수 (Fields)

- 객체의 **상태**나 **속성**을 나타냅니다.
    
- 예: 고양이의 이름, 나이, 색상 등.
    

### ② 멤버 함수 (Methods)

- 객체의 **동작**이나 **기능**을 나타냅니다.
    
- 예: 먹는다, 잔다, 야옹한다 등.
    
- 클래스 내부에서 선언하고, 외부에서 `::`(범위 지정 연산자)를 사용하여 정의할 수 있습니다.
    

---

## 3. 접근 제어자 (Access Specifiers)

캡슐화를 구현하기 위해 사용하며, 클래스 멤버의 공개 범위를 결정합니다.

- `public`: 클래스 외부 어디서든 접근 가능. (보통 함수에 사용)
    
- `private`: 클래스 내부 멤버 함수에서만 접근 가능. (보통 변수에 사용, **데이터 보호**)
    
- `protected`: 상속 관계에서 자식 클래스에게만 접근 허용.
    

---

## 💻 실전 코드: Car 클래스 예제

파일 분할(헤더/소스)과 멤버 함수 중복 정의(오버로딩)를 포함한 표준 구조입니다.

### 📝 car.h (헤더 파일)

```cpp
#pragma once
#include <string>

using namespace std;

class Car {
private:
    int speed;
    int gear;
    string color;

public:
    int getSpeed();            // 게터(Getter)
    void setSpeed(int s);      // 세터(Setter)
    void setSpeed(double s);   // 함수 중복 정의(Overloading)
    void honk();
};
```

### 📝 car.cpp (멤버 함수 본체)

```cpp
#include <iostream>
#include "car.h"

using namespace std;

int Car::getSpeed() {
    return speed;
}

void Car::setSpeed(int s) {
    if (s >= 0) speed = s; // 데이터 유효성 검사 가능
}

void Car::setSpeed(double s) {
    speed = (int)s;
}

void Car::honk() {
    cout << "빵빵!" << endl;
}
```

### 📝 main.cpp (실행 파일)

```cpp
#include <iostream>
#include "car.h"

using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    Car myCar;               // 객체 생성
    myCar.setSpeed(80);      // 멤버 함수 호출
    myCar.honk();

    cout << "현재 속도: " << myCar.getSpeed() << endl;
    return 0;
}
```

---

# 🧊 클래스의 동적 생성 (Dynamic Allocation)

> [!abstract] **핵심 요약**
> 
> - **키워드:** `new` (생성 및 할당), `delete` (해제)
>     
> - **메모리 영역:** **힙(Heap)** 영역에 할당됩니다. (일반 변수는 스택에 할당)
>     
> - **특징:** 개발자가 직접 생성과 소멸 시점을 결정할 수 있습니다.
>     

---

## 1. 정적 생성 vs 동적 생성 비교

|**구분**|**정적 생성 (Static)**|**동적 생성 (Dynamic)**|
|---|---|---|
|**선언 방식**|`Car myCar;`|`Car* pCar = new Car();`|
|**메모리 위치**|스택 (Stack)|**힙 (Heap)**|
|**접근 방식**|마침표 `.` 연산자|**화살표 `->` 연산자**|
|**생명 주기**|함수 종료 시 자동 소멸|**`delete`를 하기 전까지 생존**|

---

## 2. 주요 연산자 및 사용법

### ① 생성: `new` 연산자

객체를 힙에 할당하고, 그 객체의 **주소값**을 반환합니다. 따라서 반드시 **포인터 변수**로 받아야 합니다.

```cpp
Car* pCar = new Car(); // 기본 생성자 호출
```

### ② 접근: `->` (화살표 연산자)

포인터를 통해 객체의 멤버에 접근할 때 사용합니다.
```cpp
pCar->setSpeed(100);
cout << pCar->getSpeed();
```

### ③ 해제: `delete` 연산자

동적으로 생성된 객체는 함수가 끝나도 자동으로 사라지지 않습니다. 사용이 끝나면 반드시 메모리를 수동으로 비워줘야 합니다. (**메모리 누수 방지**)
```cpp
delete pCar; // 소멸자 호출 및 메모리 해제
pCar = nullptr; // (권장) 해제 후 포인터를 비워둠
```

---

## 💻 실전 코드 예시 (Car 클래스 활용)

```cpp
#include <iostream>
#include <string>

using namespace std;

class Car {
private:
    int speed;
public:
    Car() : speed(0) { cout << "차량이 생성되었습니다.\n"; }
    ~Car() { cout << "차량이 소멸되었습니다.\n"; } // 소멸자
    void setSpeed(int s) { speed = s; }
    int getSpeed() { return speed; }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);

    // 1. 객체 하나를 동적 생성
    Car* myCar = new Car();
    
    myCar->setSpeed(120);
    cout << "현재 속도: " << myCar->getSpeed() << "\n";

    // 2. 객체 배열을 동적 생성
    Car* carArray = new Car[3]; // 3개의 차량 객체 생성
    carArray[0].setSpeed(50);   // 배열 요소 접근은 . 사용 가능

    // 3. 반드시 해제 필요
    delete myCar;         // 단일 객체 해제
    delete[] carArray;    // 객체 배열 해제 (괄호 주의)

    return 0;
}
```

---

## ⚠️ 주의사항 (Mistakes to avoid)

1. **메모리 누수(Memory Leak):** `new`를 하고 `delete`를 하지 않으면 프로그램이 종료될 때까지 메모리를 계속 점유합니다.
    
2. **댕글링 포인터(Dangling Pointer):** `delete`를 한 뒤에도 포인터 변수가 주소값을 갖고 있는 경우입니다. 해제 후에는 `pCar = nullptr;` 처리를 하는 것이 안전합니다.
    
3. **이중 해제(Double Free):** 이미 해제된 메모리를 다시 `delete`하면 프로그램이 충돌합니다.