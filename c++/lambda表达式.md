# 一.lambda表达式的定义

lambda表达式是C++11中引入的，它是一种匿名函数。使用lambda表达式，可以简化我们的编程工作。

**lambda的语法定义：**

```plain
[capture list] (params list) mutable exception -> return type { function body }
```
* [capture list]：捕捉外部变量列表，它出现在lambda函数的开始处，是Lambda表达式的引出符，编译器根据该引出符判断接下来的代码是否是lambda函数。捕捉列表能够捕捉上下文中的变量以供Lambda函数使用
* (params list)：参数列表，与普通函数的参数列表一样，如果不需要参数传递，则可以连同括号“()”一起省略；
* mutable：mutable修饰符，可以省略。默认情况下，lambda函数总是一个const函数，mutable可以取消其常量性，在使用该修饰符时，参数列表不可省略，即使参数为空；
* exception：用于指定函数抛出的异常，如抛出整数类型的异常，可以使用 throw(int)；
* -> return type：返回类型，在不需要返回值时可以连同符号”->”一起省略，而且，在返回类型明确的情况下，也可以省略该部分，编译器会自动推导返回类型；
* { function body }：函数体，基本和普通函数一样。区别在于：不仅可以使用参数列表的参数，还可以使用捕捉列表中的变量；

# 二.捕捉列表详解

捕捉列表描述了上下文中哪些数据可以被Lambda使用，以及使用方式（值传递或引用传递），捕捉列表由多个捕捉项组成，并以逗号分隔，具体有一下几种方式：

* [var] 表示值传递方式捕捉变量var
* [=] 表示值传递方式捕捉所有父作用域的变量（包括this）
* [&var] 表示引用传递捕捉变量var
* [&] 表示引用传递方式捕捉所有父作用域的变量（包括this）
* [this] 表示值传递方式捕捉当前的this指针

捕捉列表还可以进行组合，例如以下方式：

* [=, &a, &b] 表示以引用传递的方式捕捉变量a和b，以值传递方式捕捉其它所有变量
* [&, a, this] 表示以值传递的方式捕捉变量a和this，引用传递方式捕捉其它所有变量

不过要注意捕捉列表不允许变量重复传递，否则会导致编译时期的错误，例如：

* [=, a] 这里已经以值传递方式捕捉了所有变量，但是重复捕捉a了，会报错的
* [&, &this] 这里&已经以引用传递方式捕捉了所有变量，再捕捉this也是一种重复

**注意：值传递方式参数在lambda表达式中值不能被修改，引用传递方式参数在lambda表达式内部修改和表达式外部的修改会相互影响**

```c++
//值传递示例，var变量在lambda表达式中相当于拷贝了一份，不会再收到外部变量影响
void Widget::func()
{
    int var = 10;
    auto getVar = [=]{ return var; };
    var = 20;
    std::cout << "var:" << getVar() << endl; //结果为11
}

//引用传递示例，var变量在lambda表达式内部修改和表达式外部的修改会相互影响
void Widget::func2()
{
    int var = 10;
    auto getVar = [&]{ ++var; return var; };
    std::cout << "var:" << getVar() << endl; //结果为11
    var = 20;
    std::cout << "var:" << getVar() << endl; //结果为21
}
```

# 三.lambda的一些运用举例

**事实上，lambda表达式就是一个函数对象。** 当编写了一个lambda表达式的时候，编译器将该表达式翻译成一个未命名类的未命名对象。具体看下1和2的举例

1. 在泛型算法中的使用lambda表达式，以下为排序算法几种不同用法的对比，其结果是一样的
```c++
bool sortFunc(int a, int b){ return a > b; }

class SortClass
{
public:
    bool operator()(int a, int b){ return a > b; }
};

//将vector排序
void Widget::func()
{
    std::vector<int> vec{ 4, 1, 5, 2, 3, 6 };
    std::sort(vec.begin(), vec.end(), sortFunc); //使用普通函数
    std::sort(vec.begin(), vec.end(), SortClass()); //使用类对象
    std::sort(vec.begin(), vec.end(), [](int a, int b)->bool{return a > b; }); //使用lambda表达式
}
```
在以上这种没有捕获参数的情况下，lambda表达式产生的类就和SortClass类似，有一个重载的函数调用运算符
2. 使用值传递的举例，在这种情况下，lambda表达式就等同于AddClass的实现
```c++
class AddClass
{
public:
    AddClass(int a, int b) : m_a(a), m_b(b) {}
    int operator()(const int c) const
    {
        return m_a + m_b + c;
    }
private:
    int m_a;
    int m_b;
};

void Widget::func()
{
    int a = 10;
    int b = 20;
    int c = 30;
    auto addfun = [=] (const int c ) -> int { return a+b+c; };
    int ret = addfun(c);
    cout << ret << endl; //60

    AddClass addClass(a, b);
    ret = addClass(c);
    cout << ret << endl; //60
}
```

3. mutable修饰符的使用举例
```c++
void Widget::func()
{
    int val = 0;
    // auto const_val_lambda = [=](){ val = 3; }; //编译器检测为语法错误
    auto mutable_val_lambda = [=]() mutable{
        val = 3;
        cout<<val<<endl; //结果为3
    };
    mutable_val_lambda();
    cout<<val<<endl; //结果为0
}
```

4. 在qt信号槽中使用lambda，前提是在.pro文件中加入：CONFIG+=c++11
```c++
void Widget::initUI()
{
    QPushButton *button = new QPushButton(this);
    QObject::connect(button, &QPushButton::clicked, []() {
        qDebug() << "this is button";
    });
}
```