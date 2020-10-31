## 基本用法同结构体

```c
/* 1.s,i同一个内存
   2.s_tr1.i，按照int类型解析内存空间
   3.s_tr1.s，按照short类型解析内存空间
 */
union STR1 {
	short s;			//8		
	int i;				//4		
};						//12	

int main()
{
	union STR1 s_tr1;
	printf("sizeof(s_tr1) = %d\n", sizeof(s_tr1));	// 4

	s_tr1.i = 4;
	printf("s_tr1.s = %d\n", s_tr1.s);		//4
	printf("s_tr1.i = %d\n", s_tr1.i);		//4
	printf("&s_tr1.s = %d\n", &s_tr1.s);	//
	printf("&s_tr1.i = %d\n", &s_tr1.i);	//两个地址相等，指向同一块内存
	return 0;
}
```
### 不同点
- union共用一块内存单元，对内存有多种不同的解析方式。结构体成员彼此独立，被打包在一起。
- 不存在内存对齐，只有一个内存空间。
- 内存取决于元素占用最大内存的大小
