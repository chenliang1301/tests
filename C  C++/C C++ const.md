C
const 使变量具有**只读**特性 本质还是**变量**，会分配空间
编译器有效
运行期无效
如何定义常量：枚举，而非const

C++
符号表（编译过程中编译器的数据结构中有个表）放入常量
**编译器**发现常量时从符号表中替换
有时候会分配空间 
	1修饰全局变量，在需要他的文件需要使用，extern const int a;
	2用取地址符号时，const int i= 1； *(&i) = 2:

宏定义与C++
宏定义：预处理器处理，单纯文本替换，无类型和作用域
C++:编译器处理const，类型检查，作用于检查



```cpp
#include <stdio.h>

void f()
{
    #define a 3       /* 预处理器直接替换，编译器发现时3 */
    const int b = 4;
}

void g()
{
    printf("a = %d\n", a);  
    //printf("b = %d\n", b);
}

int main()
{
    const int A = 1;
    const int B = 2;
    int array[A + B] = {0};   /* C中出错，变量不可当做数组长度，c++中对的，他是常量 */
    int i = 0;
    
    for(i=0; i<(A + B); i++)
    {
        printf("array[%d] = %d\n", i, array[i]);
    }
    
    f();
    g();
    
    return 0;
}


```

大总结：
C中const修饰的是只读变量
C++中为常量
为了兼容C有时候会分配空间extern和&
