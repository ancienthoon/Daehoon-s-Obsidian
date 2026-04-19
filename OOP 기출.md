```cpp
#include<iostream>
using namespace std;

class Point
{
public:
    int x, y;
    Point(int xx =2, int yy = 4) : x(xx), y(yy) 
    {
	    //생성자다 근데 2, 4로 초기화를 한
    }
    void print()//x, y 값을 출력하는 함수다
    {
        cout << x << " " << y << endl;
    }
};

void cha(Point &a, Point b)
{
    Point t;
    t.x = a.x;
    a.x = b.x;
    b.x = t.x;

    t.y = a.y;
    a.y = b.y;
    b.y = t.y;
}
int main()
{
    Point a(3,6), b;//(3, 6), (2, 4)
    cha(b, a); //b(2, 4 그대로), a(3, 6 값 복사하기) 그리고 왼쪽끼리 오른쪽끼리 스왑한다
    //그러면 b(3, 6) a(2, 4)
    //함수 소멸
    //b는 그대로 a는 값이 소멸되서 디포트 값으로 되돌아감
    a.print(); //3 6
    b.print(); //3 6
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

class Bat
{
    int bb;
public:
    Bat(int m = 100) : bb(m) 
    {//디폴트 생성자는 100을 bb에 넣는다
    }
    void bil(Bat ot, int a);//void다 근데 하나는 class가 들어가고, 하나는 a가 들어감

    void print() //출력하는 함수다
    {
	    cout << bb << endl;
    }
};
/*
함수 기능 외부에서 구현한다
bb에서 a를 뺀다
그 안에 있는 class는 a*10을 더한다
*/
void Bat::bil(Bat ot, int a)
{
    bb -= a;
    ot.bb += a*10;
} 


int main(void)
{
    Bat a1(2000), a2;//2000, 100
    a1.bil(a2, 500);
    /*
    a1에는 a2(100), 500을 넣는다
    1500(예는 값을 직접 변경해줬는데)
    5600(이놈은 참조를 안하서 함수 빠져나오면 100으로 다시 바뀜)
    */
    a1.print();//1500
    a2.print();//100
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

int& funcOne(int& var)//참조자 함수, 참조가 매개변수
{
    var++;
    return var;
}

int main() 
{
    int n = 100;//n선언
    int k = funcOne(n);//k는 n에다가 어떠한 조치를 취한 것이다
    
    /*
    이제 설명한다
    
    매개변수가 참조자 이므로 값이 그대로 들어간다(100)
    그리고 1을 더해준 다음 해당 값을 참조자로 반환을 한다(101)
    n이 반환을 해주면서 101 - 102로 변한다
    */

    n++;//n = 101
    cout << n << " " << k << endl;//101 102
    //
}
```

```cpp
#include<iostream>
using namespace std;

class Complex
{
    double real, image;
    static int count;//함수를 빠져나가도 기억한다
public://생성자 : 0, 0 그리고 카운트 1 증가
    Complex(double r = 0, double i = 0) : real(r), image(i)
    {
        count++;//생성자 카운트 1 증가
    }
    ~Complex()
    {//소멸자 (카운트 1 감소)
        count--;
    }
    /*
    반환값이 있는 함수다
    하나는 그대로 다른 하나는 복사본
    */
    int GetRealAdd(Complex &a, Complex b)
    {
        double r = a.real + b.real;
        return r;
    }       
    int GetCount()
    {
        return count;
    }
};
int Complex::count = 0;//카운트는 0으로 초기화 한다

int main(){
    Complex com1(1.0, 2.0), com2(3.0, 4.0);//이렇게 초기화함 그리고 cnt는 각각 1
    double ret = com1.GetRealAdd(com1, com2);
    /*
    com1은 그대로 com2는 복사본
    r = 4.0
    ret = 4
    */
    Complex* pcom4 = new Complex;//얘는 동적 class다
    cout << com1.GetCount() << endl;//1
    delete pcom4;
    cout << com1.GetCount() << endl;//0
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

class Point
{
    int x, y;
public:
    Point(int a, int b) : x(a), y(b)//값을 a, b로 초기화
    {
    }
    int GetX()
	{    
	return x;
	}
    int GetY()
	{    
		return y;
	}
};

class Rect
{
    Point leftUp, rightLow;//포인터를 여기서 불러온다
    int color;
public:
    Rect(int a, int b, int c, int d, int e);//매개변수 5개
    void ShowRect();
};

Rect::Rect(int a, int b, int c, int d, int e) : leftUp(a,b), rightLow(c, d), color(e)
{

}                                                                                                   
void Rect::ShowRect() 
{
    cout << "좌상단점 좌표 :" << leftUp.GetX() <<" " << leftUp.GetY() << "\n";
    cout << "우하단점 좌표 :" << rightLow.GetX() <<" " << rightLow.GetY() << "\n";
    cout << "면색 :" << this->color << "\n";
      
}

int main() 
{
    Rect a(10, 20, 30, 40, 1), b(10, 10, 20, 20, 2);
    a.ShowRect();
    b.ShowRect();
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

class myclass{
    int a, b;
public:
    myclass(int x = 0, int y = 0) : a(x), b(y) {
        cout << "생성자 호출\n";
    }
    myclass(const myclass & obj) : a(obj.a), b(obj.b) {
        cout << "복사생성자 호출\n";
    }
    ~myclass()
    {
        cout << "소멸자 호출\n";
    }
};

myclass Value(myclass obj)
{
    myclass t;
    t = obj;
    return t;
}

int main(void)
{
    myclass n1(1, 1), n2(2, 0);
    myclass n3 = Value(n2);
    myclass *n4 = new myclass;
    *(n4) = Value(n2);
    delete n4;
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

class myclass{
    int a, b;
    static int n;
public:
    myclass(int x = 0, int y = 0) : a(x), b(y) {
        n++;
    }
    myclass(const myclass & obj) : a(obj.a), b(obj.b) {
        n--;
    }
    ~myclass()
    {
        n--;
    }
    void Print()
    {
        cout << n << "\n";
    }
};

int myclass:: n = 0;

myclass Value(myclass obj)
{
    myclass t;
    t = obj;
    return t;
}

int main(void)
{
    myclass n1(1, 1), n2(2, 0);
    myclass n3 = Value(n2);
    myclass *n4 = new myclass;
    *(n4) = Value(n2);
    n4->Print();
    delete n4;
    n1.Print();
    return 0;
}
```

```cpp
#include<iostream>
using namespace std; 

class Point{
    int x, y;
public:
    Point(int a, int b) : x(a), y(b) {}//너는 생성자
    
    /*
    obj를 그대호 가져온다(근데 값은 변경 못함)
    그리고 각각 가져온 x, y값으로 초기화
    */
    Point(const Point &obj) : x(obj.x), y(obj.y) {
        cout << "P\n";
    }
};

class Rec{
    Point p1, p2;
public:
    Rec(Point pp1, Point pp2) : p1(pp1), p2(pp2) {}
    Rec(const Rec &obj) :p1(obj.p1), p2(obj.p2) {
        cout <<"R\n";
    }
};

int main(void)
{
    Point pa(10, 20), pb(100, 200);//point 3개 선언 pc(100, 200)
    Point pc = pb;
    Rec r1(pa, pb);
    Rec r2(pb, pa);
    Rec r3 = r1;

    return 0;
}
```