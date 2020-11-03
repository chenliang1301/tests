# 字符串三部分

> const char *p = "linux";
> 	-
> 	- (1)指向字符串的指针----4字节
> - (2)字符串：linux---- 5byte
> - (3)字符串结束符标志（不属于字符串）	：‘\0’	--- 1byte

## 如何存储字符串

> - const char *p = "linux";
> - char p[] = "linux";

## sizeof()与strlen()

> - sizeof()是关键字，返回类型和变量**所占内存大小**，预处理计算结果。
> - strlen();是C库函数，原型 sizte_t strlen(const char *);返回字符串长度，不包括‘\0’。
> 	- 实现原理：**计数直到碰到数值0。**
> - 注意：‘\0’（数值0） 、0（数值0）、‘0’（图形0，数值为0x48）
> 	- '\0' = 0;	  转义字符，指的是编码值为0 表示空字符

```c

```c

    char p[5] = "lin";
    char *b = "lin";
    printf("sizeof(p) = %d\n",sizeof(p));   //5
    printf("sizeof(b) = %d\n", sizeof(b));  //4
    printf("sizeof(lin) = %d\n", sizeof("lin"));    //4`

```

```

> - sizeof()表示开辟内存的大小

```

```c

 	char* b4 = {0};
    printf("strlen(p4) = %d\n", strlen(p4));   //0

```

- strlen() 返回字符串长度，到‘\0’结束
- 定义数组要确定大小，或者用malloc替换

## char p[] = "linux";

- 开辟6byte内存的字符数组，相当于char p[] = {'l','i','n','u','x','\0'};

## char *p = "linux";

- 定义字符指针p（栈或数据段），指向6byte内存的字符串“linux”（定义在代码段），字符串首地址（‘l’地址赋值p）；开辟了10byte内存
- 总结：
    - 字符数组自带空间，存内容。
	- 字符指针存字符串地址
	- 字符串自带首字符地址
