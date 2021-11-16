# 第11课-enum、sizeof、typedef分析

11-1.c

```c
#include<stdio.h>
enum{

	ARRAY_SIZE = 10
};

enum color{

	RED = 123,
	GREEN = 456,
	BLUE = 789
};

void printf_color(enum color c){

	switch(c){

		case RED : 
			printf("RED=%d\n",c);
			break;
		case GREEN : 
			printf("GREEN=%d\n",c);
			break;
		case BLUE : 
			printf("BLUE=%d\n",c);
			break;		
		default :
			break;
	}
}

void printf_array(){

	int array[ARRAY_SIZE] = {0};
	int i=0;
	for(i=0;i<ARRAY_SIZE;i++){
		array[i] = i+1;
	}
	for(i=0;i<ARRAY_SIZE;i++){
		printf("%d",array[i]);
	}
	printf("\n");
}

int main(){

	enum color t = RED;
	printf_array();
	printf_color(t);
	return 0;
}
```

![%E7%AC%AC11%E8%AF%BE-enum%E3%80%81sizeof%E3%80%81typedef%E5%88%86%E6%9E%90%204150e557f9354bf198a7b80fc9828911/Untitled.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216439.png)

1、枚举的使用，为常量二准备

11-2.c

```c
#include<stdio.h>

int fun(){

	printf("helloworld\n");
	return 1;
}

int main(){

	int var = 0;
	int size = sizeof(var++);
	printf("var=%d,size=%d\n",var,size);
	printf("sizeof(fun())=%d\n",sizeof(fun()));
	return 0;
}
```

![%E7%AC%AC11%E8%AF%BE-enum%E3%80%81sizeof%E3%80%81typedef%E5%88%86%E6%9E%90%204150e557f9354bf198a7b80fc9828911/Untitled%201.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216440.png)

1、sizeof并不是函数值，只是标识符

2、sizeof在编译时期运行，编译期用数字替代

3、sizeof（）里面的内容在编译期不起作用

10-5.c

```c
#include<stdio.h>

typedef int INT32;

struct SoftArray{

	INT32 len;
	INT32 array[];
};
typedef struct SoftArray SA;
SA sa ;

struct STR{

	int i;
	int j;
	short s;
	char c;
}ss;

typedef struct STR _isc;

_isc dd;

typedef struct {

	char cc;
	short sh;
	INT32 ii;
}_csi;

_csi ee;

typedef struct STR2 _isc2;
struct STR2{

	_isc2* n;//为什么要加上*????????
	short s;
	char c;
}ss1;

int main(){

	printf("_isc:%d,%d,%c",dd.i,dd.s,dd.c);

	
	return 0;
}
```

![%E7%AC%AC11%E8%AF%BE-enum%E3%80%81sizeof%E3%80%81typedef%E5%88%86%E6%9E%90%204150e557f9354bf198a7b80fc9828911/Untitled%202.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216441.png)

1、不完全类型声明，只能定义指向不完整类型的指针，指针是和平台相关的。

2、typedef TYPE name;