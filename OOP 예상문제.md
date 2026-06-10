## 중간 범위

### 1번

```cpp
class Room 
{
    int width, height;
    static int totalArea;
public:
    Room(int w, int h) : width(w), height(h) 
    {
        totalArea += area();
    }
    ~Room() { totalArea -= area(); }
    void expand(int& w, int h) {
        totalArea -= area();
        width += w;
        height += h;
        w = width;
        totalArea += area();
    }
    int area() { return width * height; }
    static int getTotal() { return totalArea; }
};
int Room::totalArea = 0;

int main() 
{
    int n = 2;
    Room a(10, 5), b(3, 4);
    cout << Room::getTotal() << endl;
    a.expand(n, 3);
    cout << n << endl;
    {
        Room c(n, 2);
        cout << Room::getTotal() << endl;
    }
    cout << Room::getTotal() << endl;
}
```

### 2번

```cpp
class Calc 
{
    int val;
public:
    Calc(int v) : val(v) {}
    Calc& add(int n) { val += n; return *this; }
    Calc& mul(int n) { val *= n; return *this; }
    int get() { return val; }
};

int main() {
    Calc a(5), b(3);
    a.add(3).mul(2).add(1);
    b.mul(4).add(b.get());
    cout << a.get() << " " << b.get() << endl;
}
```

### 3번

```cpp
class Item {
    string name;
    int price, qty;
public:
    Item(string n, int p, int q) : name(n), price(p), qty(q) {}
    void restock(Item& other) {
        qty += other.qty;
        other.qty = 0;
    }
    int total() { return this->price * this->qty; }
    void print() {
        cout << name << " " << qty << " " << total() << endl;
    }
};

int main() {
    Item a("apple", 100, 10);
    Item b("apple", 100, 5);
    Item c = a;
    a.restock(b);
    c.restock(a);
    a.print();
    b.print();
    c.print();
}
```

### 4번

```cpp
class Stack {
    int arr[5];
    int top;
public:
    Stack() : top(-1) {}
    void push(int v) { arr[++top] = v; }
    int pop() { return arr[top--]; }
    int peek() { return arr[top]; }
};

int main() {
    Stack s;
    s.push(10);
    s.push(20);
    s.push(30);
    cout << s.pop() << endl;
    s.push(40);
    cout << s.peek() << endl;
    cout << s.pop() << endl;
    cout << s.pop() << endl;
}
```

### 5번

```cpp
class Player {
    string name;
    int hp, atk;
public:
    Player(string n, int h, int a) : name(n), hp(h), atk(a) {}
    void attack(Player& target) {
        target.hp -= this->atk;
    }
    void print() {
        cout << name << " " << hp << endl;
    }
};

int main() {
    Player a("knight", 100, 30);
    Player b("mage", 80, 50);
    Player c = b;
    a.attack(b);
    b.attack(a);
    c.attack(a);
    a.print();
    b.print();
    c.print();
}
```

### 6번

```cpp
class Point {
    int x, y;
public:
    Point(int a, int b) : x(a), y(b) {}
    void move(int& dx, int dy) {
        x += dx;
        y += dy;
        dx = x;
    }
    int getX() { return x; }
    int getY() { return y; }
};

class Line {
    Point start, end;
    static int count;
public:
    Line(int x1, int y1, int x2, int y2)
        : start(x1, y1), end(x2, y2) { count++; }
    ~Line() { count--; }
    void movePoints(int& dx, int dy) {
        start.move(dx, dy);
        end.move(dx, dy);
    }
    void print() {
        cout << start.getX() << " " << start.getY() << " "
             << end.getX() << " " << end.getY() << endl;
    }
    static int getCount() { return count; }
};
int Line::count = 0;

int main() {
    int n = 5;
    Line a(0, 0, 10, 10);
    a.movePoints(n, 3);
    cout << n << endl;
    a.movePoints(n, 2);
    cout << n << endl;
    a.print();
}
```

### 7번

```cpp
class Box {
    int val;
public:
    Box() : val(0) {}
    Box(int v) : val(v) {}
    void set(int v) { this->val = v; }
    int get() { return this->val; }
};

Box& bigger(Box& a, Box& b) {
    if (a.get() >= b.get()) return a;
    return b;
}

int main() {
    Box a(10), b(30), c(20);
    bigger(a, b).set(50);
    bigger(b, c).set(bigger(a, c).get() + 10);
    cout << a.get() << " " << b.get() << " " << c.get() << endl;
}
```

### 8번

```cpp
class Date {
    int yy, mm, dd;
public:
    Date(int y, int m, int d) : yy(y), mm(m), dd(d) {}
    Date(int y) : yy(y), mm(1), dd(1) {}
    void add(Date& d) {
        yy += d.yy;
        mm += d.mm;
        dd += d.dd;
    }
    void print() {
        cout << yy << " " << mm << " " << dd << endl;
    }
};

int main() {
    Date a(2000, 6, 15);
    Date b(1);
    Date c = a;
    a.add(b);
    c.add(a);
    a.print();
    c.print();
}
```

### 9번

```cpp
class Node {
    int val;
    static int count;
public:
    Node() : val(0) { count++; }
    Node(int v) : val(v) { count++; }
    ~Node() { count--; }
    int get() { return val; }
    static int getCount() { return count; }
};
int Node::count = 0;

int main() {
    Node a(10);
    cout << Node::getCount() << endl;
    {
        Node b(20), c;
        Node d = b;
        cout << Node::getCount() << endl;
    }
    cout << Node::getCount() << endl;
}
```

### 10번

```cpp
class Score {
    int val;
public:
    Score() : val(0) {}
    Score(int v) : val(v) {}
    void set(int v) { val = v; }
    int get() { return val; }
};

void change(Score& a, Score b) {
    a.set(a.get() + b.get());
    b.set(100);
}

int main() {
    Score a(10), b(20);
    change(a, b);
    cout << a.get() << " " << b.get() << endl;
}
```

### 11번

```cpp
class Item {
    int val;
public:
    Item(int v = 0) : val(v) {
        cout << "생성자\n";
    }
    Item(const Item& obj) : val(obj.val) {
        cout << "복사생성자\n";
    }
    ~Item() { cout << "소멸자\n"; }
    int get() { return val; }
};

Item add(Item a, Item& b) {
    a.val += b.get();
    return a;
}

int main() {
    Item n1(10), n2(20);
    Item n3 = add(n1, n2);
    cout << n3.get() << endl;
}
```

### 12번

```cpp
class Bank {
    string owner;
    int balance;
public:
    Bank(string o, int b) : owner(o), balance(b) {}
    void deposit(int n) { balance += n; }
    void transfer(Bank& to, int n) {
        balance -= n;
        to.deposit(n);
    }
    void print() {
        cout << owner << " " << balance << endl;
    }
};

int main() {
    Bank a("Kim", 1000), b("Lee", 500);
    Bank c = a;
    a.transfer(b, 300);
    c.transfer(a, 100);
    a.print();
    b.print();
    c.print();
}
```
## 기말 범위

### 1번

```cpp
class Point {
    int x, y;
public:
    Point(int a = 0, int b = 0) : x(a), y(b) {}
    Point(const Point& p) : x(p.x), y(p.y) {
        cout << "Point 복사생성자\n";
    }
    ~Point() { cout << "Point 소멸자\n"; }
    int getX() { return x; }
};

class Shape {
protected:
    Point pos;
public:
    Shape(int x, int y) : pos(x, y) {
        cout << "Shape 생성자\n";
    }
    Shape(const Shape& s) : pos(s.pos) {
        cout << "Shape 복사생성자\n";
    }
    virtual ~Shape() { cout << "Shape 소멸자\n"; }
    virtual void draw() = 0;
};

class Rect : public Shape {
public:
    Rect(int x, int y) : Shape(x, y) {
        cout << "Rect 생성자\n";
    }
    Rect(const Rect& r) : Shape(r) {
        cout << "Rect 복사생성자\n";
    }
    ~Rect() { cout << "Rect 소멸자\n"; }
    void draw() { cout << "Rect draw\n"; }
};

void render(Shape& s) { s.draw(); }

int main() {
    Rect r1(1, 1);
    Shape* p = new Rect(r1);
    render(*p);
    delete p;
}
```

### 2번

```cpp
class MyException {
    string msg;
public:
    MyException(string m) : msg(m) {}
    virtual string getMsg() { return msg; }
};

class BigException : public MyException {
public:
    BigException(string m) : MyException(m) {}
    string getMsg() { return "Big: " + MyException::getMsg(); }
};

class Box {
    int val;
    static int count;
public:
    Box(int v = 0) : val(v) {
        if (v > 100) throw BigException("값 초과!");
        if (v < 0)   throw MyException("음수 불가!");
        count++;
    }
    ~Box() { count--; }
    int get() { return val; }
    static int getCount() { return count; }
};
int Box::count = 0;

int main() {
    try {
        Box a(10), b(200);
    }
    catch (BigException& e) {
        cout << e.getMsg() << "\n";
    }
    catch (MyException& e) {
        cout << e.getMsg() << "\n";
    }
    cout << Box::getCount() << "\n";

    try {
        Box c(50), d(-1);
    }
    catch (BigException& e) {
        cout << e.getMsg() << "\n";
    }
    catch (MyException& e) {
        cout << e.getMsg() << "\n";
    }
    cout << Box::getCount() << "\n";
}
```

### 3번

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    Animal(const Animal& a) { cout << "Animal 복사생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {
    int val;
    static int count;
public:
    Dog(int v = 0) : val(v) {
        if (v < 0) throw "음수 불가!";
        count++;
        cout << "Dog 생성자\n";
    }
    Dog(const Dog& d) : Animal(d), val(d.val) {
        count++;
        cout << "Dog 복사생성자\n";
    }
    ~Dog() {
        count--;
        cout << "Dog 소멸자\n";
    }
    void speak() { cout << "멍멍\n"; }
    Dog& operator=(const Dog& d) {
        val = d.val * 2;
        return *this;
    }
    int get() { return val; }
    static int getCount() { return count; }
};
int Dog::count = 0;

Animal& getAnimal(Dog& d) { return d; }

int main() {
    try {
        Dog d1(10), d2(20);
        cout << Dog::getCount() << "\n";
        Animal& a = getAnimal(d1);
        a.speak();
        d1 = d2;
        cout << d1.get() << "\n";
        Dog d3(-1);
    }
    catch (const char* e) {
        cout << e << "\n";
    }
    cout << Dog::getCount() << "\n";
}
```

### 4번

```cpp
int& addOne(int& x) {
    x++;
    return x;
}

int main() {
    int a = 10, b = 20;
    int& r = addOne(a);
    r = 50;
    int k = addOne(b);
    b++;
    cout << a << " " << b << " " << r << " " << k << "\n";
}
```

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    Animal(const Animal& a) { cout << "Animal 복사생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {
public:
    Dog() { cout << "Dog 생성자\n"; }
    Dog(const Dog& d) : Animal(d) { cout << "Dog 복사생성자\n"; }
    ~Dog() { cout << "Dog 소멸자\n"; }
    void speak() { cout << "멍멍\n"; }
};

Animal& getAnimal(Dog& d) {
    d.speak();
    return d;  // 참조 반환!
}

int main() {
    Dog d;
    Animal& a = getAnimal(d);
    a.speak();
    Animal b = a;
    b.speak();
}
```

### 5번

```cpp
class MyException {
    string msg;
public:
    MyException(string m) : msg(m) {}
    string getMsg() { return msg; }
};

class Box {
    int val;
    static int count;
public:
    Box(int v = 0) : val(v) {
        if (v < 0) throw MyException("음수 불가!");
        count++;
    }
    Box(const Box& b) : val(b.val) { count++; }
    ~Box() { count--; }
    Box& operator=(const Box& b) {
        val = b.val * 2;
        return *this;
    }
    Box operator+(const Box& b) {
        return Box(val + b.val);
    }
    int get() { return val; }
    static int getCount() { return count; }
};
int Box::count = 0;

Box& bigger(Box& a, Box& b) {
    return (a.get() >= b.get()) ? a : b;
}

int main() {
    try {
        Box a(10), b(20);
        cout << Box::getCount() << "\n";
        bigger(a, b).operator=(a);
        cout << a.get() << " " << b.get() << "\n";
        Box c = a + b;
        cout << Box::getCount() << "\n";
        Box d(-1);
    }
    catch (MyException& e) {
        cout << e.getMsg() << "\n";
    }
    cout << Box::getCount() << "\n";
}
```

### 6번

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {
public:
    Dog() { cout << "Dog 생성자\n"; }
    ~Dog() { cout << "Dog 소멸자\n"; }
    void speak() { cout << "멍멍\n"; }
};

int main() {
    try {
        Animal* a = new Dog();
        a->speak();
        Animal b = *a;
        b.speak();
        delete a;
        throw "오류!";
    }
    catch (const char* e) {
        cout << e << "\n";
    }
}
```

### 7번

```cpp
class Shape {
    static int count;
public:
    Shape() { count++; }
    virtual ~Shape() { count--; }
    virtual void draw() = 0;
    static int getCount() { return count; }
};
int Shape::count = 0;

class Circle : public Shape {
    int r;
public:
    Circle(int r) : r(r) {
        if (r <= 0) throw r;
    }
    void draw() { cout << "Circle " << r << "\n"; }
};

int main() {
    Shape* arr[2] = {};
    try {
        arr[0] = new Circle(5);
        arr[1] = new Circle(-1);
    }
    catch (int e) {
        cout << "예외: " << e << "\n";
        arr[1] = new Circle(3);
    }
    cout << Shape::getCount() << "\n";
    for (int i = 0; i < 2; i++) {
        arr[i]->draw();
        delete arr[i];
    }
    cout << Shape::getCount() << "\n";
}
```

### 8번

```cpp
class MyException {
    string msg;
public:
    MyException(string m) : msg(m) {}
    string getMsg() { return msg; }
};

class ChildException : public MyException {
public:
    ChildException(string m) : MyException(m) {}
};

class Vector {
    double x, y;
    static int count;
public:
    Vector(double x = 0, double y = 0) : x(x), y(y) {
        if (x < 0 || y < 0) throw ChildException("음수 불가!");
        count++;
    }
    Vector(const Vector& v) : x(v.x), y(v.y) { count++; }
    ~Vector() { count--; }
    Vector operator+(const Vector& v) {
        return Vector(x + v.x, y + v.y);
    }
    void display() { cout << x << " " << y << "\n"; }
    static int getCount() { return count; }
};
int Vector::count = 0;

int main() {
    try {
        Vector v1(1, 2), v2(3, 4);
        cout << Vector::getCount() << "\n";
        Vector v3 = v1 + v2;
        cout << Vector::getCount() << "\n";
        Vector v4(-1, 2);
    }
    catch (ChildException& e) {
        cout << e.getMsg() << "\n";
    }
    catch (MyException& e) {
        cout << e.getMsg() << "\n";
    }
    cout << Vector::getCount() << "\n";
}
```

### 9번

```cpp
class Box {
    int val;
public:
    Box(int v = 0) : val(v) {}
    Box operator+(const Box& b) {
        return Box(val + b.val);
    }
    Box& operator=(const Box& b) {
        val = b.val * 2;
        return *this;
    }
    int get() { return val; }
};

int main() {
    Box a(10), b(20);
    Box c = a + b;
    a = c;
    c = a;
    cout << a.get() << " " << b.get() << " " << c.get() << "\n";
}
```

### 10번

```cpp
class Vector {
    double x, y;
public:
    Vector(double x = 0, double y = 0) : x(x), y(y) {}
    Vector operator+(const Vector& v) {
        return Vector(x + v.x, y + v.y);
    }
    bool operator==(const Vector& v) {
        return x == v.x && y == v.y;
    }
    friend Vector operator*(double a, const Vector& v);
    friend Vector operator*(const Vector& v, double a);
    void display() { cout << x << " " << y << "\n"; }
};

Vector operator*(double a, const Vector& v) {
    return Vector(a * v.x, a * v.y);
}
Vector operator*(const Vector& v, double a) {
    return Vector(v.x * a, v.y * a);
}

int main() {
    Vector v1(1, 2), v2(3, 4);
    Vector v3 = v1 + v2;
    Vector v4 = 2.0 * v3;
    Vector v5 = v3 * 2.0;
    cout << (v4 == v5) << "\n";
    v4.display();
}
```

### 11번

```cpp
class Num {
    int val;
public:
    Num(int v = 0) : val(v) {}
    Num(const Num& n) : val(n.val) {
        cout << "복사생성자\n";
    }
    Num operator+(const Num& n) {
        return Num(val + n.val);
    }
    Num& operator=(const Num& n) {
        val = n.val * 2;
        return *this;
    }
    bool operator==(const Num& n) {
        return val == n.val;
    }
    int get() { return val; }
};

int main() {
    Num a(10), b(20);
    Num c = a + b;
    a = b;
    cout << a.get() << " " << c.get() << "\n";
    cout << (a == c) << "\n";
}
```

### 12번

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {
public:
    Dog() { cout << "Dog 생성자\n"; }
    ~Dog() { cout << "Dog 소멸자\n"; }
    void speak() { cout << "멍멍\n"; }
};

int main() {
    Animal* a = new Dog();
    Animal b = *a;
    a->speak();
    b.speak();
    delete a;
}
```

### 13번

```cpp
class Shape {
    static int count;
public:
    Shape() { count++; }
    virtual ~Shape() { count--; }
    virtual void draw() = 0;
    static int getCount() { return count; }
};
int Shape::count = 0;

class Circle : public Shape {
public:
    void draw() { cout << "Circle\n"; }
};

class Rect : public Shape {
public:
    void draw() { cout << "Rect\n"; }
};

void render(Shape& s) { s.draw(); }

int main() {
    Shape* arr[3];
    arr[0] = new Circle();
    arr[1] = new Rect();
    arr[2] = new Circle();
    cout << Shape::getCount() << "\n";
    for (int i = 0; i < 3; i++)
        render(*arr[i]);
    delete arr[1];
    cout << Shape::getCount() << "\n";
    for (int i = 0; i < 3; i += 2)
        delete arr[i];
    cout << Shape::getCount() << "\n";
}
```

### 14번

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    Animal(const Animal& a) { cout << "Animal 복사생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {
public:
    Dog() { cout << "Dog 생성자\n"; }
    Dog(const Dog& d) : Animal(d) { cout << "Dog 복사생성자\n"; }
    ~Dog() { cout << "Dog 소멸자\n"; }
    void speak() { cout << "멍멍\n"; }
};

void print(Animal& a) { a.speak(); }

int main() {
    Animal* a = new Dog();
    Dog* d = new Dog();
    print(*a);
    print(*d);
    Animal b = *d;
    b.speak();
    delete a;
    delete d;
}
```

### 15번

```cpp
class Engine {
public:
    Engine() { cout << "Engine 생성자\n"; }
    Engine(const Engine& e) { cout << "Engine 복사생성자\n"; }
    ~Engine() { cout << "Engine 소멸자\n"; }
};

class Car {
    Engine engine;  // has-a
public:
    Car() { cout << "Car 생성자\n"; }
    ~Car() { cout << "Car 소멸자\n"; }
    Engine getEngine() { return engine; }
};

int main() {
    Car a;
    Engine e = a.getEngine();
    cout << "끝\n";
}
```

### 16번

```cpp
class Animal {
public:
    Animal() { cout << "Animal 생성자\n"; }
    Animal(const Animal& a) { cout << "Animal 복사생성자\n"; }
    virtual ~Animal() { cout << "Animal 소멸자\n"; }
    virtual void speak() { cout << "...\n"; }
};

class Dog : public Animal {  // is-a
public:
    Dog() { cout << "Dog 생성자\n"; }
    Dog(const Dog& d) : Animal(d) { cout << "Dog 복사생성자\n"; }
    ~Dog() { cout << "Dog 소멸자\n"; }
    void speak() { cout << "멍멍\n"; }
};

int main() {
    Animal* a = new Dog();
    a->speak();
    Animal b = *a;  // 함정!
    b.speak();
    delete a;
}
```

### 17번

```cpp
class Point {
public:
    Point() { cout << "Point 생성자\n"; }
    Point(const Point& p) { cout << "Point 복사생성자\n"; }
    ~Point() { cout << "Point 소멸자\n"; }
};

class Shape {
    Point pos;      // has-a
    static int count;
public:
    Shape() : pos() { 
        count++;
        cout << "Shape 생성자\n"; 
    }
    Shape(const Shape& s) : pos(s.pos) {
        count++;
        cout << "Shape 복사생성자\n";
    }
    virtual ~Shape() { 
        count--;
        cout << "Shape 소멸자\n"; 
    }
    virtual void draw() = 0;
    static int getCount() { return count; }
};
int Shape::count = 0;

class Rect : public Shape {  // is-a
public:
    Rect() { cout << "Rect 생성자\n"; }
    Rect(const Rect& r) : Shape(r) { cout << "Rect 복사생성자\n"; }
    ~Rect() { cout << "Rect 소멸자\n"; }
    void draw() { cout << "Rect draw\n"; }
};

void render(Shape& s) {  // 참조!
    s.draw();
}

int main() {
    Shape* arr[2];
    arr[0] = new Rect();
    arr[1] = new Rect();
    cout << Shape::getCount() << "\n";
    for (int i = 0; i < 2; i++)
        render(*arr[i]);
    delete arr[0];
    cout << Shape::getCount() << "\n";
    delete arr[1];
    cout << Shape::getCount() << "\n";
}
```

[[OOP 과제]]
