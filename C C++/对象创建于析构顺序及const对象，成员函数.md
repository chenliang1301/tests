# 单个对象创建时构造函数顺序

- 1父类
- 2成员变量
- 3自身

## 析构的顺序相反

```cpp

#include <stdio.h>

class Member
{
    const char* ms;
public:
    Member(const char* s)
    {
        printf("Member(const char* s): %s\n", s);

        ms = s;
    }
    ~Member()
    {
        printf("~Member(): %s\n", ms);
    }
};

class Test
{
    Member mA;
    Member mB;
public:
    Test() : mB("mB"), mA("mA")
    {
        printf("Test()\n");
    }
    ~Test()
    {
        printf("~Test()\n");
    }
};

Member gA("gA");

int main()
{
    Test t;

    return 0;
}

```

打印
Member(const char* s): gA
Member(const char* s): mA
Member(const char* s): mB
Test()
~Test()
~Member(): mB
~Member(): mA
~Member(): gA

- 栈对象和全局对象的创建和析构，类似入栈出栈顺序
- 堆对象的析构发生在delete时

### const对象特性

- const对象为只读对象
- 只读对象的成员变量不可改变
- 编译阶段不可改，运行阶段无效
- 只读对象只能调用const成员函数
-

### const成员函数

- const成员函数不能修改成员变量

```cpp

#include <stdio.h>

class Test
{
    int mi;
public:
    int mj;
    Test(int i);
    Test(const Test& t);
    int getMi();//const成员函数声明
};

Test::Test(int i)
{
    mi = i;
}

Test::Test(const Test& t)//t 为test引用，t为只读对象，只能调用const成员函数
{
    mi = t.getMi();
}

int Test::getMi()//const成员函数定义
{
    //mi = 2;//const成员函数不能修改成员变量
    return mi;
}

int main()
{
    const Test t(1);
    //t.mj = 3;//error 只读对象的成员变量值不可改变，编译阶段不可改，运行阶段无效
    //printf("t.getMi() = %d\n", t.getMi());//1

    return 0;
}

```

#### 对象

- 对象由成员变量和成员方法构成
- 对象由数据和函数构成
    -  数据位于栈、堆、全局数据区
    - 函数位于代码段

#### 结论

- 每个对象都有自己的独立属性（成员变量）
- 所有对象共享类的方法（成员函数）
- 方法能直接访问对象的属性
- 方法中隐藏参数this，用于指代当前对象

```cpp

#include <stdio.h>

class Test
{
    int mi;
public:
    int mj;
    Test(int i);
    Test(const Test& t);
    int getMi();//const成员函数声明
    void print();
};

Test::Test(int i)
{
    mi = i;
}

Test::Test(const Test& t)//t 为test引用，t为只读对象，只能调用const成员函数
{
    mi = t.mi;//拷贝构造函数调用自己的成员变量，
}

int Test::getMi()//const成员函数定义
{
    //mi = 2; //const成员函数不能修改成员变量
    return mi;
}

void Test::print()//this指针指向当前对象，值为对象地址，成员函数隐藏this指针
{
    printf("this = %d\n", this);//this
}

int main()
{
     Test t1(1);
     Test t2(2);
     Test t3(3);
     printf("t1.getMi() = %d\n", t1.getMi());
     printf("&t1 = %d\n", &t1);
     t1.print();//与上地址相同
     printf("t2.getMi() = %d\n", t2.getMi());
     printf("&t2 = %d\n", &t2);
     t2.print();//与上地址相同
     printf("t3.getMi() = %d\n", t3.getMi());
     printf("&t3 = %d\n", &t3);
     t3.print();//

    //t.mj = 3;//error 只读对象的成员变量值不可改变，编译阶段不可改，运行阶段无效
    //printf("t.getMi() = %d\n", t.getMi());//1

    return 0;
}

```

## 小结

- 对象的析构顺序与构造函数顺序相反
- const关键字能修饰对象，为只读对象
- 只读对象只能调用const成员函数
- 所有对象共享类的成员函数
- 成员函数隐藏this指针用于表示当前对象
