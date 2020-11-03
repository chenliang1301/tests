# const volatile Register

# const修饰符意味：只读

- 使用：
    - 1、const修饰的局部变量，不能当左值，但可以通过指针进行修改。存在栈上只读变量
    - 2、const修饰的全局变量（具有全局声明周期：如static修饰的局部变量），不可以当左值，不可以使用指针进行修改，否则会导致程序奔溃
    - 3、const 本质修饰的是 只读变量，将变量放在只读存储区。
- 目的：
    - 1 、告诉了用户这个参数的应用目的
    - 2、使编译器很自然地保护那些不希望被改变的参数。
    - 3、在定时器、多线程、共享内存时需要注意。

```c

#include<stdio.h>

const int n =1;

int main(){
    int i = 1;
    //const int n =1;
    int *p = (int*)&n;

    printf("i=%d,n=%d\n",i,n);
    i = 2;
    *p = 2;
//	n = 2;
    printf("i=%d,n=%d\n",i,n);
    return 0;
}

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328121050803.png)

```c

#include<stdio.h>

const char* fun(const int i){

    //i = 5;
    return "hello world!" ;
}

int main(){

    const char* pc;
    char* pp= "HELLO WORLD!";
    int n = 0;
    pc = fun(4);
    printf("fun=%s\n",fun(4));
    pp[6] = '-';
    //pp[7] = "-";
    for(n=0;n<10;n++){
        printf("%c",pp[n]);
    }
    printf("\n");


    return 0;
}

```

![](https://img-blog.csdnimg.cn/20200328121143231.png)

举例：
1） const int a;
2）int const a;
3）const int *a ;
4）int * const a;
5）int const * a const;

1）2）一样效果：定义一个整型指针不可赋值
3）a是一个指向常整型数的指针（整型数是不可修改的，但指针可以）
4）a是一个指向整型数的常指针（整型数是可修改的，但指针不可以）
5）a是一个指向常整型数的常指针（整型数是不可修改的，指针不可以）

# volatile 防止“变化”

-  强制编译器减少优化，必须每次从内存中取值
-  使用场景：定时器，多线程共享和任务变量
    - 多线程应用中被几个任务共享的变量
    - 一个中断服务子程序中会访问到的非自动变量（全局变量、静态变量）
    - 并行设备的硬件寄存器？？（数据接收口）
- 举例：
    - const volatile int i = 0;可以这样定义么？
        - 可以的，这是容易被改变的值，去内存中取出来。而且不能放在等号左边进行修改。
    - int square(volatile int *ptr)
{
       	 	return *ptr * *ptr;
} 定义有错误么？
        - 容易被外部中断或多线程更改，应该加个const。
 # Register
  - 尽可能将变量存在CPU内部寄存器，提高效率
