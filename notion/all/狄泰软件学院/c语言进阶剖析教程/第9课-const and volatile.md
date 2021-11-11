# 第9课-const and volatile

9-1.c

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

![%E7%AC%AC9%E8%AF%BE-const%20and%20volatile%20bf05618af3e5465a9609fbe6f584de70/Untitled.png](D:\08 git\Notes\notion\all\狄泰软件学院\c语言进阶剖析教程\images\Untitled-1636385452857.png)

1、const修饰的局部变量，不能当左值，但可以通过指针进行修改。存在栈上只读变量

2、const修饰的全局变量（具有全局声明周期：如static修饰的局部变量），不可以当左值，不可以使用指针进行修改，否则会导致程序奔溃

3、const 本质修饰的是 只读变量，将变量放在只读存储区。

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

![%E7%AC%AC9%E8%AF%BE-const%20and%20volatile%20bf05618af3e5465a9609fbe6f584de70/Untitled%201.png](D:\08 git\Notes\notion\all\狄泰软件学院\c语言进阶剖析教程\images\Untitled 1-1636385455749.png)

1、字符串字面量；存在只读存储区，在程序中需要用const char*修饰

2、无论是局部还是全局，都需要用const char*修饰，或隐式修饰

![%E7%AC%AC9%E8%AF%BE-const%20and%20volatile%20bf05618af3e5465a9609fbe6f584de70/Untitled%202.png](D:\08 git\Notes\notion\all\狄泰软件学院\c语言进阶剖析教程\images\Untitled 2-1636385457263.png)