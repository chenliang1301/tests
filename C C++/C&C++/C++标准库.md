# 标准库的打印

```cpp

#include <stdio.h>

const char endl = '\n';

class Console
{
public:
    Console& operator << (int i)//
    {
        printf("%d\n", i);
        return *this;//返回当前对象
    }

    Console& operator << (char c)//
    {
        printf("%c", c);
        return *this;//返回当前对象
    }

    Console& operator << (const char* s)//
    {
        printf("%s", s);
        return *this;//返回当前对象
    }

    Console& operator << (double d)//
    {
        printf("%f", d);
        return *this;//返回当前对象
    }

};

Console cout;

int main()
{

    cout << 1 << endl;

    cout << "chen" << endl;
    //==>cout.operator << (1);//1输出到命令行

    double a = 0.1;
    double b = 0.2;

    cout << a + b << endl;

    return 0;
}

```

- C++标准库有类库和函数库组成的集合
- C++标准库中定义的类和对象都位于std命名空房间中
- C++标准库头文件不带.h
- C++标准库涵盖C库的功能

```cpp

#include <cstdio>//C库

#include <cstring>

#include <cstdlib>

#include <cmath>

using namespace std;

int main()
{
    printf("hello world\n");

    char * p = (char *)malloc(18);
    strcpy(p, "che");

    double a = 3;
    double b = 4;
    double c = sqrt(a*a + b*b);

    printf("c = %f\n", c);

    free(p);

    return 0;
}

```

## C++ 输入输出

```cpp

#include <iostream>//C库

#include <cmath>

using namespace std;

int main()
{
    cout << "hello" << endl;

    double a = 3;
    double b = 4;

    cout << "input a:";
    cin >> a;//键盘输入到a

    cout << "input b:";
    cin >> b;//键盘输入到a

    double c = sqrt(a*a + b*b);

    cout << "c = " << c << endl;

    return 0;
}

```

## 小结

- C++标准库是类库和函数库组成的集合
- C++标准库包含经典算法和数据结构的实现
- C++标准库涵盖C库功能
- C++标准库位于std命名空间
