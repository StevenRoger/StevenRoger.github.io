---
title: 对象和类
description: C++中对象和类的一些基础概念，过程性编程和面向对象编程
categories:
- C++
tags:
- C++
---

## 面向对象编程特性
- 抽象
- 封装和数据隐藏
- 继承
- 多态
- 代码的可重用性

## C++中的类
> 类是一种将抽象转换为用户自定义烈性的C++工具，它将数据表示和操纵数据的方法组合成一个整洁的包。

## 类的构造函数和析构函数
### 构造函数
用于构造新对象，将值赋给它们的数据成员。名称与类名相同。
调用方式：
```c++
//显式调用
Stock food = Stock("World Cabbage", 250, 1.25);

//隐式调用
Stock garment("Furry Mason", 50, 2.5);

//和new一起使用
Stock *pstock = new Stock("Elementstock Games", 18, 19.0);
```

`默认构造函数`是在未提供显示初始值时，用来创建对象的构造函数，默认构造函数没有参数，如果提供了非默认构造函数，编译器则不会提供默认构造函数，这样做，是为了避免创建未初始化的对象。
如：
```c++
Stock fluffy_the_cat;
```

定义默认构造函数的两种方法：
```c++
//给已有构造函数的所有参数提供默认值
Stock(const string & co = "Error", int n = 0, double pr = 0.0);

//函数重载
Stock();
```

### 析构函数
当构造函数创建对象后，程序负责跟踪该对象，直到其过期为止，对象过期时，程序将调用一个特殊的成员函数-析构函数，利用它来完成清理工作，如果没有显式的提供析构函数，编译器将提供隐式析构函数。
析构函数没有返回值和声明类型，也没有参数，其原型只有一种方式：`~类名();`。析构函数通常不需要显示调用。
- 静态存储类对象，析构函数在程序结束时被调用；
- 自动存储类对象，析构函数在执行完代码块（scope）时自动被调用；
- 如果是通过new创建的对象，它将驻留内存，直到调用delete来释放内存时，析构函数自动被调用。

C++11 列表初始化：需要提供与某个构造函数的参数列表匹配的内容。
```c++
//如下构造函数
Stock::Stock(const std::string & co, int n = 0, double pr = 0.0);

//如下对象声明
Stock hot_tip = {"Derivatives Plus Plus", 100, 45.0};
Stock jock {"Sport Age Storage, Inc"};
Stock temp {};
```

## const成员函数
const成员函数，保证函数不会修改调用对象。就像应尽可能将const引用和指针用作函数形参一样，只要类方法不修改调用对象，就应将其声明为const。
声明：
```c++
//promises not to change invoking object
void show() const;
```
定义：
```c++
void Stock::show() const;
```

## this指针
每个成员函数（包括构造函数和析构函数）都有一个this指针。this指针指向调用对象。如果方法需要引用整个调用对象，则可以使用表达式`*this`。在函数的括号后面使用const限定符将this限定为const，这样将不能使用this来修改对象的值。
如果一个函数的返回值类型是该调用对象的引用，则可以使用`return *this;`的方式返回。
要返回的并不是this，因为this是对象的地址，而对象的本身是`*this(将解除引用运算符用于指针，将得到指针指向的值)`。

## 对象
```c++
class Stock
{
private:
    static int m_type;

public:
    Stock();
    ~Stock();
    static void testStaticFun()
    {
        std::cout << "m_type = " << m_type << std::endl; //静态成员函数不能访问非静态成员
    }
};

int Stock::m_type = 2;

int _tmain(int argc, _TCHAR* argv[])
{
    Stock test{};
    cout << " size of test: " << sizeof (test)<<endl;//输出 size of test: 1
	return 0;
}
```
C++标准规定类的大小不为0，空类的大小为1，当类不包含虚函数和非静态数据成员时，其对象大小也为1。 如果在类中声明了虚函数（不管是1个还是多个），那么在实例化对象时，编译器会自动在对象里安插一个指针指向虚函数表VTable，在32位机器上，一个对象会增加4个字节来存储此指针，它是实现面向对象中多态的关键。

静态成员属于类作用域，但不属于类对象，它的生命周期和普通的静态变量一样，程序运行时进行分配内存和初始化，程序结束时则被释放。所以不能在类的构造函数中进行初始化。

static成员函数没有this指针，所以静态成员函数不可以访问非静态成员。