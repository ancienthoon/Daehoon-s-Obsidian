## 🗂️ 목차

- [[#1장]]
- [[#2장]]
- [[#3장]]
- [[#4장]]
- [[#5장]]
- [[#6장]]
- [[#7장]]
- [[#8장]]
- [[#9장]]
- [[#10장]]
- [[#11장]]
- [[#12장]]

---

## 1장
## 2장
## 3장
## 4장
## 5장
## 6장
## 7장

## 8장

> 상속을 이용해서 AND NOT OR XOR 게이트를 구현하는 과제


```cpp
#include <iostream>
using namespace std;

// 기반 클래스
class Gate
{
protected:
    bool x, y, z;

public:
    Gate()
    {
        x = false;
        y = false;
        z = false;
    }

    void inputSet(bool xx, bool yy)
    {
        x = xx;
        y = yy;
    }

    // 순수 가상 함수 (파생 클래스에서 반드시 구현)
    virtual void op() = 0;

    virtual ~Gate() {}
};

// AND Gate
class ANDGate : public Gate
{
public:
    void op()
    {
        z = x && y;  // AND 연산

        // 출력: 입력(x, y) / 결과(z)
        cout << "AND Gate" << endl;
        cout << "Input: (" << x << ", " << y << ")" << endl;
        cout << "Output: " << z << endl;
        cout << endl;
    }
};

// OR Gate
class ORGate : public Gate
{
public:
    void op()
    {
        z = x || y;  // OR 연산

        // 출력: 입력(x, y) / 결과(z)
        cout << "OR Gate" << endl;
        cout << "Input: (" << x << ", " << y << ")" << endl;
        cout << "Output: " << z << endl;
        cout << endl;
    }
};

// XOR Gate
class XORGate : public Gate
{
public:
    void op()
    {
        z = x ^ y;  // XOR 연산

        // 출력: 입력(x, y) / 결과(z)
        cout << "XOR Gate" << endl;
        cout << "Input: (" << x << ", " << y << ")" << endl;
        cout << "Output: " << z << endl;
        cout << endl;
    }
};

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);

    // AND Gate 테스트
    cout << "=== AND Gate ===" << endl;
    ANDGate andGate;

    andGate.inputSet(true, true);
    andGate.op();

    andGate.inputSet(true, false);
    andGate.op();

    andGate.inputSet(false, false);
    andGate.op();

    // OR Gate 테스트
    cout << "=== OR Gate ===" << endl;
    ORGate orGate;

    orGate.inputSet(true, true);
    orGate.op();

    orGate.inputSet(true, false);
    orGate.op();

    orGate.inputSet(false, false);
    orGate.op();

    // XOR Gate 테스트
    cout << "=== XOR Gate ===" << endl;
    XORGate xorGate;

    xorGate.inputSet(true, true);
    xorGate.op();

    xorGate.inputSet(true, false);
    xorGate.op();

    xorGate.inputSet(false, false);
    xorGate.op();

    return 0;
}
```


> 상속을 이용해서 원과 직선을 그리는 과제(그래픽 필요X)

```cpp
#include <iostream>
using namespace std;

// Point 클래스 (기본 좌표)
class Point
{
private:
    int x, y;

public:
    Point(int xx = 0, int yy = 0) : x(xx), y(yy) {}

    int getX() const { return x; }
    int getY() const { return y; }

    void display() const
    {
        cout << "(" << x << ", " << y << ")";
    }
};

// Shape 기반 클래스
class Shape
{
protected:
    Point start;   // 시작점
    Point end;     // 끝점

public:
    Shape(int x1, int y1, int x2, int y2)
        : start(x1, y1), end(x2, y2) {
    }

    virtual void Draw() = 0;  // 순수 가상 함수
    virtual ~Shape() {}
};

// Line 클래스
class Line : public Shape
{
public:
    Line(int x1, int y1, int x2, int y2)
        : Shape(x1, y1, x2, y2) {
    }

    void Draw()
    {
        cout << "직선: ";
        cout << "시작점 ";
        start.display();
        cout << ", 끝점 ";
        end.display();
        cout << endl;
        cout << "직선 그린다" << endl;
    }
};

// Circle 클래스
class Circle : public Shape
{
public:
    Circle(int x1, int y1, int x2, int y2)
        : Shape(x1, y1, x2, y2) {
    }

    void Draw()
    {
        cout << "원: ";
        cout << "좌상단점 ";
        start.display();
        cout << ", 우하단점 ";
        end.display();
        cout << endl;
        cout << "원 그린다" << endl;
    }
};

// main 함수
void p8_2()
{
    Circle a(1, 1, 5, 5);      // 원: 좌상단(1,1), 우하단(5,5)
    Line b(5, 5, 9, 9);         // 직선: 시작점(5,5), 끝점(9,9)

    a.Draw();
    cout << endl;
    b.Draw();
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);

    p8_2();

    return 0;
}
```

## 9장

> 상속과 가상함수를 이용해서 원과 직선을 그리는 과제(그래픽 필요X)

```cpp
// oop_91_20253334.txt

#include <iostream>
using namespace std;

// ─────────────────────────────────────
// Point 클래스
// ─────────────────────────────────────
class Point {
private:
    int x, y;

public:
    Point(int x = 0, int y = 0) : x(x), y(y) {}

    int getX() const { return x; }
    int getY() const { return y; }
};

// ─────────────────────────────────────
// Shape 클래스 (부모)
// ─────────────────────────────────────
class Shape {
private:
    Point start;   // 좌상단점
    Point end;     // 우하단점

public:
    // 생성자: 시작점, 끝점 좌표를 인자로 받아 저장
    Shape(int x1, int y1, int x2, int y2)
        : start(x1, y1), end(x2, y2) {
    }

    // 공통 Draw(): 좌표 출력 담당
    void Draw() const {
        cout << "  좌상단점: (" << start.getX() << ", " << start.getY() << ")" << endl;
        cout << "  우하단점: (" << end.getX() << ", " << end.getY() << ")" << endl;
    }

    // 순수 가상함수: 자식이 반드시 구현해야 함
    virtual void Draw(int dummy) const = 0;

    virtual ~Shape() {}
};

// ─────────────────────────────────────
// Line 클래스 (자식)
// ─────────────────────────────────────
class Line : public Shape {
public:
    Line(int x1, int y1, int x2, int y2)
        : Shape(x1, y1, x2, y2) {
    }

    // 다른 부분: "직선 그린다" 출력
    // 공통 부분: Shape::Draw() 호출
    void Draw(int dummy = 0) const override {
        cout << "직선 그린다" << endl;
        Shape::Draw();   // 좌표 출력 (공통)
    }
};

// ─────────────────────────────────────
// Circle 클래스 (자식)
// ─────────────────────────────────────
class Circle : public Shape {
public:
    Circle(int x1, int y1, int x2, int y2)
        : Shape(x1, y1, x2, y2) {
    }

    // 다른 부분: "원 그린다" 출력
    // 공통 부분: Shape::Draw() 호출
    void Draw(int dummy = 0) const override {
        cout << "원 그린다" << endl;
        Shape::Draw();   // 좌표 출력 (공통)
    }
};

// ─────────────────────────────────────
// main
// ─────────────────────────────────────
void main() {
    Circle a(1, 1, 5, 5);
    Line   b(5, 5, 9, 9);

    a.Draw();   // 원 그린다 + 좌표
    b.Draw();   // 직선 그린다 + 좌표

    Shape* p;
    p = new Line(10, 10, 100, 100);
    p->Draw(0);   // 직선 그린다 + 좌표

    p = new Circle(100, 100, 200, 200);
    p->Draw(0);   // 원 그린다 + 좌표

    delete p;
}
```

## 10장

### 10_1장

> 벡터를 나타내는 Vector 클래스에 다음 연산자들을 구성하고 멤버함수로 구현

```cpp
//코드 예시
int main() {  
Vector a(1, 2), b(2, 3), c;  
c = a – b; // c = a - b; 에서 a 값 변화 없음  
cout << a << “ , “ << b << “ , “ << c;  
  
a = b; // a = a b 수행, a를 반환  operator = 함수 작성  
cout << a << “ , “ << b << “ , “ << c;  
  
a -= c; // a = a-c 수행 , a를 반환  operator-= 함수 작성  
cout << a << “ , “ << b << “ , “ << c;  
  
return 0;  
}
```

```cpp
#include <iostream>
using namespace std;

class Vector
{
private:
    int x, y;
    
public:
    // 생성자
    Vector(int xx = 0, int yy = 0) : x(xx), y(yy) {}
    
    // operator- (멤버함수): c = a - b
    // a와 b는 변화 없음, 새로운 Vector 반환
    Vector operator-(const Vector& other) const
    {
        Vector result(x - other.x, y - other.y);
        return result;
    }
    
    // operator+= (멤버함수): a += b
    // a를 변화시키고 자신을 반환
    Vector& operator+=(const Vector& other)
    {
        x += other.x;
        y += other.y;
        return *this;
    }
    
    // operator-= (멤버함수): a -= c
    // a를 변화시키고 자신을 반환
    Vector& operator-=(const Vector& other)
    {
        x -= other.x;
        y -= other.y;
        return *this;
    }
    
    // operator<< (비멤버함수): cout << a
    friend ostream& operator<<(ostream& os, const Vector& v);
    
    // getter (for debugging)
    int getX() const { return x; }
    int getY() const { return y; }
};

// operator<< 구현 (비멤버함수)
ostream& operator<<(ostream& os, const Vector& v)
{
    os << "(" << v.x << ", " << v.y << ")";
    return os;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);
    
    Vector a(1, 2), b(2, 3), c;
    
    // c = a - b; (a, b 변화 없음)
    c = a - b;
    cout << a << " , " << b << " , " << c << endl;
    // 출력: (1, 2) , (2, 3) , (-1, -1)
    
    // a += b; (a를 반환)
    a += b;
    cout << a << " , " << b << " , " << c << endl;
    // 출력: (3, 5) , (2, 3) , (-1, -1)
    
    // a -= c; (a를 반환)
    a -= c;
    cout << a << " , " << b << " , " << c << endl;
    // 출력: (4, 6) , (2, 3) , (-1, -1)
    
    return 0;
}
```

### 10_3장

> 10장 연습문제 Programming 1번(426쪽)  == 연산자는 두 객체 멤버 배열 공간 크기 다르면 false 
```cpp
//예시 코드
int main() 
{ 
	Array a1(10), a2(10), a3(10); 
	a1[0] = 1; a1[1] = 2; a1[2] = 3; 
	a1[3] = 4; a2[0] = 1; a2[1] = 2; 
	a2[2] = 3; a2[3] = 4; a3 = a1; a3[3] = 5; 
	cout << ＂a1 배열은 : ＂ << a1 << endl; 
	
	/* 
	1 2 3 4 0 0 ... cout << ＂a2 배열은 : ＂ << a2 << endl; 
	1 2 3 4 0 0 ... cout << ＂a3 배열은 : ＂ << a3 << endl; 
	1 2 3 5 0 0 ... cout << ＂a1 == a2 을 중복 정의 : ＂ << (a1 == a2) << endl;          1 cout << ＂a1 != a3 을 중복 정의 : ＂ << (a1 != a3) << endl; 
	1 cout << ＂a3 = a1 을 중복 정의 : " << (a3 = a1) << endl; 
	1 2 3 4 0 0 ... return 0; } 
	*/
```

```cpp
#include <iostream>
using namespace std;

class Array
{
private:
    int *data;      // 저장 공간
    int size;       // data 배열 크기 저장
    
public:
    // 생성자
    Array(int size = 10)
    {
        this->size = size;
        data = new int[size];
        
        // 배열 초기화 (0으로)
        for (int i = 0; i < size; i++)
            data[i] = 0;
    }
    
    // 소멸자
    ~Array()
    {
        delete[] data;
    }
    
    // getSize()
    int getSize() const
    {
        return size;
    }
    
    // operator= (할당 연산자)
    // a3 = a1;
    Array& operator=(const Array& other)
    {
        // 자기 자신에 대한 할당 체크
        if (this == &other)
            return *this;
        
        // 기존 메모리 해제
        delete[] data;
        
        // 새로운 크기로 메모리 할당
        size = other.size;
        data = new int[size];
        
        // 배열 내용 복사
        for (int i = 0; i < size; i++)
            data[i] = other.data[i];
        
        return *this;
    }
    
    // operator[] (배열 접근)
    // a1[0] = 1;
    int& operator[](int index)
    {
        if (index < 0 || index >= size)
        {
            cout << "배열 범위 초과!" << endl;
            return data[0];  // 임시방편
        }
        return data[index];
    }
    
    // operator[] const (읽기 전용)
    const int& operator[](int index) const
    {
        if (index < 0 || index >= size)
        {
            cout << "배열 범위 초과!" << endl;
            return data[0];
        }
        return data[index];
    }
    
    // operator== (동등 비교)
    // a1 == a2
    bool operator==(const Array& other) const
    {
        // 크기 다르면 false
        if (size != other.size)
            return false;
        
        // 모든 원소 비교
        for (int i = 0; i < size; i++)
        {
            if (data[i] != other.data[i])
                return false;
        }
        
        return true;
    }
    
    // operator!= (부등 비교)
    // a1 != a3
    bool operator!=(const Array& other) const
    {
        return !(*this == other);
    }
    
    // operator<< (출력)
    friend ostream& operator<<(ostream& os, const Array& arr);
};

// operator<< 구현 (비멤버 함수)
ostream& operator<<(ostream& os, const Array& arr)
{
    for (int i = 0; i < arr.size; i++)
    {
        os << arr.data[i];
        if (i < arr.size - 1)
            os << " ";
    }
    return os;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);
    
    Array a1(10), a2(10), a3(10);
    
    a1[0] = 1;
    a1[1] = 2;
    a1[2] = 3;
    a1[3] = 4;
    
    a2[0] = 1;
    a2[1] = 2;
    a2[2] = 3;
    a2[3] = 4;
    
    a3 = a1;
    a3[3] = 5;
    
    cout << "a1 배열은 : " << a1 << endl;
    // 출력: 1 2 3 4 0 0 0 0 0 0
    
    cout << "a2 배열은 : " << a2 << endl;
    // 출력: 1 2 3 4 0 0 0 0 0 0
    
    cout << "a3 배열은 : " << a3 << endl;
    // 출력: 1 2 3 5 0 0 0 0 0 0
    
    cout << "a1 == a2 을 중복 정의 : " << (a1 == a2) << endl;
    // 출력: 1 (true)
    
    cout << "a1 != a3 을 중복 정의 : " << (a1 != a3) << endl;
    // 출력: 1 (true)
    
    cout << "a3 = a1 을 중복 정의 : " << (a3 = a1) << endl;
    // 출력: 1 2 3 4 0 0 0 0 0 0
    
    return 0;
}
```

