C++ 中的动态内存分配
- C++ 通过new关键字进行动态内存申请
- 基于类型申请

变量申请：
Type* pointer = new Type;
//....
delete point;

Type* pointer = new Type[N];
//....
delete[] point;//释放一片内存空间
delete poinr;//释放数组首地址对饮内存空间，造成内存泄露

    p = new int[10];//p所指向的这一片数组空间，至少40个字节，、

#### new关键字与malloc函数

- new关键字是C++的一部分
- malloc是C库函数
- new是基于类型申请的
- malloc是基于字节申请的
- new申请某个类型变量是可以初始化的
- malloc不具备可初始化

new关键字初始化
int* pi = new int(1);//申请空间成功后初始化为1

#### C++命名空间

- C
    - C语言所有的全局标识符共享同一个作用域
    - 标识符可能发生冲突
- C++ 命名空间
    - 命名空间将全局作用域分成不同部分
    - 不同命名空间的标识符可以同名
    - 命名空间可相互嵌套
    - 全局作用域也叫默认命名空间
- C++ 命名空间定义
如：
namespace Name //类
{
    namespace Internal
    {

    }
}

- C++命名空间的使用
    - 使用整个命名空间：using namespace name;
    -  使用命名空间中的变量：using name::variable;
    - 使默认命名空间变量：::variable;

```cpp

#include <stdio.h>

namespace First//命名空间,类
{
    int i = 0;
}

namespace Second
{
    int i = 1;

    namespace Internal
    {
        struct P
        {
            int x;
            int y;
        };
    }
}

int main()
{
    using namespace First;
    using Second::Internal::P;

    printf("First::i = %d\n", i);//0
    printf("Second::i = %d\n", Second::i);//1

    P p = {2, 3}; //已经声明

    printf("p.x = %d\n", p.x);//2
    printf("p.y = %d\n", p.y);//3

    return 0;
}

```

- 总结
    - C++动态内存分配关键字new
    - C++动态内存分配可初始化
    - C++动态内存分配基于类型的
    - C++命名空间，解决名称冲突

