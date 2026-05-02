## 🗂️ 목차

- [[#8장]]
- [[#9장]]
- [[#10장]]
- [[#11장]]
- [[#12장]]

---

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