理论：
bool类型：占用一个字节
true：非零，表现为1
false：零，表现为0

```cpp

#include <stdio.h>

int main(int argc, char *argv[])
{
    bool b = false;
    int a = b;

    printf("sizeof(b) = %d\n", sizeof(b));//1
    printf("b = %d, a = %d\n", b, a);//0,0

    b = 3;
    a = b;

    printf("b = %d, a = %d\n", b, a);//1,1

    b = -5;
    a = b;

    printf("b = %d, a = %d\n", b, a);//1,1

    a = 10;
    b = a;

    printf("a = %d, b = %d\n", a, b);//10,1

    a = 0;
    b = a;

    printf("a = %d, b = %d\n", a, b);//0,0

    return 0;
}

```

在总结：bool修饰的变量会将值改为1或0.

三目运算符
C中不能当左值
C++中三目运算符可能返回都为变量时，可当左值，返回的是变量的引用
C++中三目运算符可能返回有常量时，不可当左值，返回的是值，常量

```cpp

#include <stdio.h>

int main(int argc, char *argv[])
{

    int a = 1l;
    int b = 22;

    (a < b ? a : b ) = 3; //，a或b的引用，对
    (a < b ? a : 1 ) = 3; //错
    printf("a = %d\tb = s%d\n", a, b); //3,255

}

```

引用
概念：已定义变量的别名
方法：Type& b = a;
如：int a = 2;
 int& b = a;
 b = 5;//a==5

```cpp

#include <stdio.h>

int main(int argc, char *argv[])
{
    int a = 4;
    int& b = a;
    float& b = a;/* 报错，强类型，必须类型一致 */
    int& b;/* 错，未定义 */
    int& b = 1;/* 对 */

    b = 5;

    printf("a = %d\n", a);//4
    printf("b = %d\n", b);//5
    printf("&a = %p\n", &a);
    printf("&b = %p\n", &b);//与a地址相同

    return 0;
}

```

 大总结：
 1bool值只能为true（1）或false（0）
 2三目运算符：可能的返回值都为变量时返回变量的引用，可作为左值。若返回可能为常量，值返回值为值，不可左值
 3C++的引用可作为变量的别名
