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