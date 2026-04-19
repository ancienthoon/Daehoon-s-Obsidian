[[OOP기본용어]]
[[생성자와 소멸자]]
[[OOP 기출]]
## 1. [Node] 클래스와 객체의 기초 (Class & Object)

> [!abstract] **개념 설명**
> 
> - **클래스(Class):** 객체를 만들기 위한 **설계도**나 **틀**입니다. (예: 붕어빵 틀, 자동차 설계도)
>     
> - **객체(Object):** 클래스를 바탕으로 실제로 메모리에 만들어진 **실체**입니다. (예: 실제 붕어빵, 내 차)
>     

### 핵심 특징

- 클래스는 `멤버 변수`(속성)와 `멤버 함수`(동작)로 구성됩니다.
    
- C언어의 구조체(`struct`)가 진화한 형태로, 데이터뿐만 아니라 데이터를 다루는 함수까지 하나로 묶은 것입니다.
    

---

## 2. [Node] 접근 제어자와 캡슐화 (Access Specifiers)

> [!info] **정보 은닉 (Information Hiding)** 클래스 내부의 중요한 데이터를 외부에서 마음대로 바꾸지 못하게 보호하는 것입니다.

### 접근 제어자의 종류

1. **private:** 클래스 내부에서만 접근 가능합니다. 보통 **멤버 변수**를 여기에 둡니다.
    
2. **public:** 클래스 외부 어디서나 접근 가능합니다. 보통 **멤버 함수**를 여기에 둡니다.
    

### 사용하는 이유

- 사용자가 잘못된 값을 넣는 것을 방지합니다. (예: 자동차 속도를 -100으로 설정하는 것 방지)
    
- 클래스 내부 구현이 바뀌어도 외부 코드를 수정할 필요가 없어 유지보수가 쉽습니다.
    

---

## 3. [Node] 멤버 함수의 선언과 정의 분리

> [!tip] **코드 가독성 높이기** 클래스 안에는 함수가 무엇을 하는지 이름만 써두고(선언), 실제 구체적인 동작은 클래스 밖에서 정의하는 방식입니다.

### 사용 방법

- 클래스 밖에서 함수를 정의할 때는 **범위 지정 연산자(`::`)**를 사용하여 "이 함수는 어느 클래스 소속이다"라고 명시합니다.
    

```cpp
#include <iostream>
#include <string>
using namespace std;

class Car {
private:
    int speed;
public:
    void set_speed(int s);
    int get_speed();
};

// 클래스 밖에서 정의
void Car::set_speed(int s) {
    speed = s;
}

int Car::get_speed() {
    return speed;
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);
    
    Car my_car;
    my_car.set_speed(80);
    cout << "현재 속도: " << my_car.get_speed() << "\n";
    
    return 0;
}
```

---

## 4. [Node] 객체 간의 상호작용 (참조자 활용)

> [!important] **핵심 원리** 함수가 객체를 매개변수로 받을 때는 원본을 직접 다루기 위해 보통 **참조자(`&`)**를 사용합니다.

### 은행 계좌 이체 예제

한 계좌에서 다른 계좌로 돈을 보낼 때, 받는 계좌 객체를 참조자로 전달하여 잔액을 직접 수정합니다.

```cpp
#include <iostream>
using namespace std;

class BankAccount {
private:
    int balance;
public:
    void set_balance(int amount) { balance = amount; }
    void deposit(int amount) { balance += amount; }
    void withdraw(int amount) { balance -= amount; }
    
    // 다른 계좌(other)를 참조자로 받음
    void transfer(BankAccount &other, int amount) {
        this->withdraw(amount);
        other.deposit(amount);
    }
    
    void print() { cout << "잔액: " << balance << "\n"; }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);
    
    BankAccount my_acc, friend_acc;
    my_acc.set_balance(10000);
    friend_acc.set_balance(5000);
    
    my_acc.transfer(friend_acc, 3000);
    
    my_acc.print();
    friend_acc.print();
    
    return 0;
}
```

---

## 5. [Node] 생성자와 소멸자 (Constructor & Destructor)

> [!abstract] **객체의 일생**
> 
> - **생성자:** 객체가 만들어질 때 **자동으로 딱 한 번 호출**되는 함수입니다. 멤버 변수의 초기값을 설정합니다.
>     
> - **소멸자:** 객체가 메모리에서 사라질 때 자동으로 호출됩니다.
>     

### 특징

- **이름:** 클래스 이름과 똑같습니다. (소멸자는 앞에 `~`가 붙음)
    
- **반환값:** 아예 없습니다. (`void`도 쓰지 않음)
    
- **중복 정의:** 생성자는 매개변수만 다르면 여러 개 만들 수 있습니다.
    

---

## 6. [Node] 초기화 리스트 (Initialization List)

> [!info] **효율적인 초기화** 생성자의 몸체(`{ }`)가 실행되기 전에 멤버 변수에 값을 미리 채워넣는 방식입니다.

### 장점

- 코드가 간결해집니다.
    
- 상수(`const`) 멤버 변수나 참조자 멤버 변수는 반드시 이 방식으로만 초기화할 수 있습니다.
    

### 문법

`생성자이름() : 멤버변수1(값1), 멤버변수2(값2) { }`

```cpp
#include <iostream>
#include <string>
using namespace std;

class Car {
private:
    int speed;
    string color;
public:
    // 초기화 리스트 사용
    Car(int s, string c) : speed(s), color(c) {
        // 몸체에서는 추가적인 작업만 수행
    }

    void show() {
        cout << color << " 자동차의 속도: " << speed << "\n";
    }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(NULL); cout.tie(NULL);
    
    Car my_car(100, "Red");
    my_car.show();
    
    return 0;
}
```