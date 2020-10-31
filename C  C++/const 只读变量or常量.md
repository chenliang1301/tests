- const常量的判别标准
	- 只有**字面量**初始化的 const常量才会进入符号表
	- 使用其他**变量**初始化的 const常量仍然是只读变量
	- voliate const修饰的常量，不会进入符号表，具有只读属性
	> 编译器不能直接确定初始值的const标识符，都作为只读变量处理

- const引用类型与初始化变量类型
	- 相同：初始化变量成为只读变量
	- 不同：生成一个新的只读变量 
	

```cpp
#include <stdio.h>

int main()
{
    const int x = 1;	//x为常量，同时分配空间，一般不使用，否则通过指针或引用使用
    const int& rx = x;  //rx为只读变量，值1，不能出现在赋值符号的左边
     
    int& nrx = const_cast<int&>(rx);  //const_cast消除只读变量的属性，nrx为去除只读属性的变量，与rx是同一个内存空间
    
    nrx = 5;
    
    printf("x = %d\n", x);//1
    printf("rx = %d\n", rx);//1
    printf("nrx = %d\n", nrx);//5
    printf("&x = %p\n", &x);//
    printf("&rx = %p\n", &rx);
    printf("&nrx = %p\n", &nrx);//三者地址相同
    
    volatile const int y = 2; //volatile 异变，只读变量，分配空间
    int* p = const_cast<int*>(&y);//去除空间的的只读属性，并取别名为p
    
    *p = 6;
    
    printf("y = %d\n", y);//6
	printf("&y = %p\n", &y);//y的地址
    printf("p = %p\n", p);//y的地址
    
    const int z = y;//只读变量
    
    p = const_cast<int*>(&z);//去除只读属性
    
    *p = 7;
    
    printf("z = %d\n", z);//7
    printf("p = %p\n", p);//z的地址
    
    char c = 'c';
    char& rc = c;
    const int& trc = c;//类型不同，生成新的只读变量*********
    
    rc = 'a';
    
    printf("c = %c\n", c);//a
    printf("rc = %c\n", rc);//a
    printf("trc = %c\n", trc);//c
    
    return 0;
}

```
#### 引用于指针的关系
> 引用 本质与指针常量
- 指针是个变量
	- 值为内存地址，不需要初始化，可保存不同的值
	- 通过指针可访问内存地址值  
	- 指针被const修饰成为常量或只读变量
- 引用只是变量的新名字
	- 对引用的操作（赋值，取地址）都会传递到对应的变量上
	- const修饰，使变量具有只读属性
	- 引用必须初始化，之后无法代表其他变量 	

- C++角度：引用是变量的新名字
- C++编译器：编译器内部使用指针常量来实现“引用”

```cpp
#include <stdio.h>

int a = 1;

struct SV
{
    int& x;
    int& y;
    int& z;
};

int main()
{
    int b = 2;
	int a = 1;
    int* pc = new int(3);
    SV sv = {a, b, *pc};
    //int& array[] = {a, b, *pc}; // &array[1] - &array[0] = ?  Expected ==> 4,成员地址之差期待为4
	//C++不支持引用数组
    
    printf("&sv.x = %p\n", &sv.x);//a地址
	printf("&a = %p\n", &a);//a地址
    printf("&sv.y = %p\n", &sv.y);//b地址
	printf("&b = %p\n", &b);//b地址
    printf("&sv.z = %p\n", &sv.z);//*pc地址
	printf("&8pc = %p\n", &(*pc));//b地址    
    delete pc;
    
    return 0;
}

```


## 小结
- 指针是变量，引用是别名
- const引用产生新的只读变量
- 编译器内部使用指针常量实现引用
- 编译器不能确定初始值的const标识符都为只读变量
