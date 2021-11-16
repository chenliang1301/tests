# sizeof && strlen

1.sizeof是运算符。以类型、函数、做参。strlen是函数，只能以char*(字符串)做参数。而且，要想得到的结果正确必须包含 ‘\0’。
2.sizeof计算结果的是分配空间的实际字节数。strlen是计算的空间中字符的个数（不包括‘\0’）
3.sizeof是在编译的时候就将结果计算出来了是类型所占空间的字节数，所以以数组名做参数时计算的是整个数组的大小。而strlen是在运行的时候才开始计算结果，这是计算的结果不再是类型所占内存的大小，数组名就退化为指针了。

[https://blog.csdn.net/magic_world_wow/article/details/80500473](https://blog.csdn.net/magic_world_wow/article/details/80500473)
另外，sizeof不能计算动态分配空间的大小如：

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
int main()
{
   char str[20] = "hello world";
   char *s = (char *)malloc(20);
   strcpy(s, str);
   printf("strlen(str)=%d\n",strlen(str));
   printf("sizeof(str)=%d\n",sizeof(str));
   printf("strlen(s)=%d\n",strlen(s));
   printf("sizeof(s)=%d\n",sizeof(s));
   free(s);
   return 0;
}
```

![sizeof%20&&%20strlen%2059e6b1cd539f49aba038931a2c75c7ab/Untitled.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162241650.png)

    最后的sizeof计算的是指针(sizeof(char *)) 的大小，为4。当适用了于一个结构类型时或变量， sizeof 返回实际的大小， 当适用一静态地空间数组， sizeof 归还全部数组的尺寸。 sizeof 操作符不能返回动态地被分派了的数组或外部的数组的尺寸。

下面我们来看一看sizeof和strlen的具体使用：

    首先看几个例子 ：
        第一个例子：


```c
char* s = "0123456789";
sizeof(s);     //结果 4    ＝＝＝》s是指向字符串常量的字符指针
sizeof(*s);    //结果 1    ＝＝＝》*s是第一个字符
strlen(s);     //结果 10   ＝＝＝》有10个字符，strlen是个函数内部实现是用一个循环计算到\0为止之前
strlen(*s);     //结果 10   ＝＝＝》错误

char s[] = "0123456789";
sizeof(s);     //结果 11   ＝＝＝》s是数组，计算到\0位置，因此是10＋1
strlen(s);     //结果 10   ＝＝＝》有10个字符，strlen是个函数内部实现是用一个循环计算到\0为止之前
sizeof(*s);    //结果 1    ＝＝＝》*s是第一个字符

char s[100] = "0123456789";
sizeof(s);     //结果是100 ＝＝＝》s表示在内存中的大小 100×1
strlen(s);     //结果是10  ＝＝＝》strlen是个函数内部实现是用一个循环计算到\0为止之前

int s[100] = "0123456789";
sizeof(s);     //结果 400  ＝＝＝》s表示再内存中的大小 100×4
strlen(s);     //错误      ＝＝＝》strlen的参数只能是char* 且必须是以‘\0‘结尾的

char q[]="abc";
char p[]="a\n";
sizeof(q),sizeof(p),strlen(q),strlen(p);\\结果是 4 3 3 2

char p[] = {'a','b','c','d','e','f','g','h'};
char q[] = {'a','b','c','d,'\0','e','f','g'};
sizeof(p);     //结果是8 ＝＝＝》p表示在内存中的大小 8×1
strlen(p);     //为一个随机值，结果与编译器有关，不同编译器结果一般不同
sizeof(q);     //结果是8 ＝＝＝》p表示在内存中的大小 8×1
strlen(q);     //结果为4 ＝＝＝》存在'\0',遇到'\0'计算停止。
```

```c
struct Stu
{
int i;
int j;
char k;
};
Stu stu;
printf（"%d\n",sizeof(Stu));  //结果 12  ＝＝＝》内存补齐
printf（"%d\n",sizeof(stu));;  //结果 12  ＝＝＝》内存补齐
```

