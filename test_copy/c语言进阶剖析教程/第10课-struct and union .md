# 第10课-struct and union

```c
#include<stdio.h>

struct TS{

};

int main(){

	struct TS t1;
	struct TS t2;
	printf("sizeof(TS)=	%d\n",sizeof(struct TS));
	printf("sizeof(t1)=	%d,&t1=%p\n",sizeof(t1),&t1);
	printf("sizeof(t2)=	%d,&t2=%p\n",sizeof(t2),&t2);
	return 0;
}
```

![%E7%AC%AC10%E8%AF%BE-struct%20and%20union%20284e8e141592490798abaee08c56bde3/Untitled.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216550.png)

1、空结构体不占用内存

2、变量地址相差1byte？？？？？？？

10-2.c

```c
#include<stdio.h>

struct SoftArray{
	int i;
	int array[];
};

int main(){

	struct SoftArray t1;
	struct SoftArray t2;
	printf("sizeof(struct SoftArray)=%d\n",sizeof(struct SoftArray));
	printf("sizeof(t1)=	%d,&t1=%p\n",sizeof(t1),&t1);
	printf("sizeof(t2)=	%d,&t2=%p\n",sizeof(t2),&t2);
	return 0;
}
```

![%E7%AC%AC10%E8%AF%BE-struct%20and%20union%20284e8e141592490798abaee08c56bde3/Untitled%201.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216551.png)

1、柔性数组最后int array[];不占内存大小，标识符

10-3.c

```c
#include<stdio.h>
#include<stdlib.h>

struct SoftArray{
	int i;
	int array[];
};

int main(){

	int n=0;
	struct SoftArray* p = NULL;
	p = (struct SoftArray*)malloc(sizeof(struct SoftArray)+5*sizeof(int));
	p->i = 5;
	
	for(n=0;n<p->i;n++){
	   	p->array[n] = n+1;
	}

	for(n=0;n<p->i;n++){
		printf("%d",p->array[n]);
	}
	p = NULL;
	printf("\n");
	
	return 0;
}
```

![%E7%AC%AC10%E8%AF%BE-struct%20and%20union%20284e8e141592490798abaee08c56bde3/Untitled%202.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216552.png)

1、柔性数组

2、可以数组长度自己分配

10-3-1.c

```c
#include<stdio.h>
#include<stdlib.h>

struct SoftArray{
	int i;
	int array[];
};

struct SoftArray* soft_array(const int size){

	struct SoftArray* sa = NULL;				
	if(size>0){
		sa = (struct SoftArray*)malloc(sizeof(struct SoftArray)+size*sizeof(int));	
		sa->i = size;	
	}
	return sa;
}

int main(){

	int n=0;
	struct SoftArray* ret = soft_array(10);
	
	for(n=0;n<ret->i;n++){
	   	ret->array[n] = n+1;
	}

	for(n=0;n<ret->i;n++){
		printf("%d",ret->array[n]);
	}
	ret = NULL;
	printf("\n");
	
	return 0;
}
```

![%E7%AC%AC10%E8%AF%BE-struct%20and%20union%20284e8e141592490798abaee08c56bde3/Untitled%203.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216553.png)

1、真正的柔性数组

10-4.c

```c
#include<stdio.h>

int system_mode(){
	union SM{
		int i;
		char c;
	};
	union SM u;
	u.i = 1;
	return u.c;
}

int main(){

	printf("system_mode:%d\n",system_mode());
	return 0;
}
```

![%E7%AC%AC10%E8%AF%BE-struct%20and%20union%20284e8e141592490798abaee08c56bde3/Untitled%204.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162216554.png)

1、小端

2、union所有数据成员共享同一个存储区