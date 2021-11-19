# 第24课-#pragram使用分析

```c
//24-1.C
#include <stdio.h>

#if defined (ANDROID20)
    #pragma message ("android 2.0")
    #define VERSION "android 2.0"
    
#elif defined (ANDROID30)
      #pragma message("android 3.0") 
      #define VERSION "android 3.0"
      
#else 
    #error printf("the "ANDROID" is not define!\n");

#endif

int main()
{
    printf("VERTION:%s\n",VERSION);
    return 0;
}

gcc -DANDROID20 24-1.c
//a.out
VERTION:android 2.0
总结：
		1、#pragma message("print message");
```

```c
//actest.h
#ifndef _ACTEST_H_
#define _ACTEST_H_

#pragma once

char* s = "hello";

#endif /* _ACTEST_H_ */

//a.out
book@www.100ask.org:~/Desktop/homework$ gcc -DANDROID20 24-1.c
24-1.c:7:13: note: #pragma message: android 2.0
     #pragma message ("android 2.0")
             ^
book@www.100ask.org:~/Desktop/homework$ ./a.out
VERTION:android 2.0
VERTION:hello

总结：
	1、用于保证头文件只被编译一次
	2、调高工作效率
```

如何计算内存对齐及结构体成员在内存里面的排布

```c
#include <stdio.h>
#pragma pack(4)
struct test1   
{             // 对齐参数   偏移地址    大小                    
    char c;   // 1           0           1
    short s;  // 2           2           2
    char c2;  // 1           4           1   
    int i;    // 4           8           4
};
#pragma pack()

#pragma pack(4)
struct test2
{
    char c;   // 1       0       1
    char c2;  // 1       1       1
    short s;  // 2       2       2  
    int i;    // 4       4       4
};
#pragma pack()

int main()
{
    struct test1 *t1;
    struct test2 *t2;    
    printf("sizeof(struct test1) = %d\n",(int)sizeof(struct test1));
    printf("sizeof(struct test2) = %d\n",(int)sizeof(struct test2)); 
    printf("sizeof(sizeof(struct test2)) = %d\n",sizeof(sizeof(struct test2))); /* sizeof decide system x86 x64 */
    printf("&(t1->c)=%p\t&(t1->s)=%p\t&(t1->c2)=%p\t&(t1->i)=%p\n",&(t1->c),&(t1->s),&(t1->c2),&(t1->i));
    printf("&(t2->c)=%p\t&(t2->c2)=%p\t&(t2->s)=%p\t&(t2->i)=%p\n",&(t2->c),&(t2->c2),&(t2->s),&(t2->i));    
    printf("&(t1)=%p\t&(t2)=%p\t\n",&(t1),&(t2));
    return 0;
}
```

总结：

- #pragma message 自定义编译消息
- #pragma once 保证头文件只编译一次
- #pragma pack 用于内存对齐

注意：

- [ ]  所定义编译器特有的