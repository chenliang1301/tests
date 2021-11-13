# 第009课_gcc和makefile

复习一下C语言中的指针

*第一步 : 所有变量都保存在内存中，我们打印一下变量的存储地址*
*第二步：所有变量都可以保存某些值,接着赋值并打印*
*第三步：使用指针：1)取值 2)移动指针*

结论：指针变量所存储的内容是所指向的变量在内存中的起始地址。
指针对连续空间进行操作时：

1）取值
2）移动指针

指针加数值的问题

抽象T *t; t是一个指针变量，里面装的是一个地址值。
经过t=t+n(数值),t的值增加了n*sizeof(T)个字节

char *pc;pc=pc+1; 

sizeof(char)=1字节,经过pc=pc+1之后，pc加了1个字节

int *pi;pi=pi+1; 

sizeof(int)=4字节,经过pi=pi+1之后，pc加了4个字节

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/39b9f5ed65a1abbae8df532af70b9b51.png](D:\08 git\notion\all\韦东山\第一期\images\39b9f5ed65a1abbae8df532af70b9b51.png)

 

char ca[3]={'A','B','C'};
char *pc = ca;

pc是指向字符数组的字符指针,pc就是数组首元素的地址,pc=&a[0]

char *pc="abc";

pc是指向字符串的字符指针,pc就是字符串"abc"的首字符'a'的地址

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/1ceb032336542c0ed8f9cb7ef1b5a6cc.png](D:\08 git\notion\all\韦东山\第一期\images\1ceb032336542c0ed8f9cb7ef1b5a6cc.png)

gcc和arm-linux-gcc的常用选项

gcc的使用方法：
gcc     [选项]      文件名

gcc常用选项：
     -v：查看gcc编译器的版本，显示gcc执行时的详细过程
     -o <file> Place the output into <file>
                  指定输出文件名为file，这个名称不能跟源文件名同名
      -E Preprocess only; do not compile, assemble or link
                  只预处理，不会编译、汇编、链接
      -S Compile only; do not assemble or link
                   只编译，不会汇编、链接
      -c Compile and assemble, but do not link
                    编译和汇编，不会链接

方式1：
        gcc hello.c 输出一个a.out，然后./a.out来执行该应用程序。

gcc -o hello hello.c 输出hello，然后./hello来执行该应用程序。

方式2：

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/2b24a886c0255c3cf562d72101ca9882.png](D:\08 git\notion\all\韦东山\第一期\images\2b24a886c0255c3cf562d72101ca9882.png)

         gcc -E -o hello.i hello.c

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/880f0764ad8a0bfd9b6b0a9d4c4fae4c.png](D:\08 git\notion\all\韦东山\第一期\images\880f0764ad8a0bfd9b6b0a9d4c4fae4c.png)

         gcc -S -o hello.s hello.i

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/08f513cff9a78cfd6402e768dcff6800.png](D:\08 git\notion\all\韦东山\第一期\images\08f513cff9a78cfd6402e768dcff6800.png)

           gcc -c -o hello.o hello.s

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/6879e55f709c11c1d8521d1d361024f9.png](D:\08 git\notion\all\韦东山\第一期\images\6879e55f709c11c1d8521d1d361024f9.png)

         gcc -o hello hello.o

方式3：

gcc -c -o hello.o hello.c
gcc -o hello hello.o

gcc会对.c文件默认进行预处理操作，-c再来指明了编译、汇编，从而得到.o文件
再通过gcc -o hello hello.o将.o文件进行链接，得到可执行应用程序。

链接就是将汇编生成的OBJ文件、系统库的OBJ文件、库文件链接起来，
最终生成可以在特定平台运行的可执行程序。

crt1.o、crti.o、crtbegin.o、crtend.o、crtn.o是gcc加入的系统标准启动文件，
对于一般应用程序，这些启动是必需的。

-lc：链接libc库文件，其中libc库文件中就实现了printf等函数。

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/56e2d650e481584cef391222c9ae3bef.png](D:\08 git\notion\all\韦东山\第一期\images\56e2d650e481584cef391222c9ae3bef.png)

gcc -v -nostdlib -o hello hello.o会提示因为没有链接系统标准启动文件和标准库文件，而链接失败。

这个-nostdlib选项常用于裸机/bootloader、linux内核等程序，因为它们不需要启动文件、标准库文件。

一般应用程序才需要系统标准启动文件和标准库文件。裸机/bootloader、linux内核等程序不需要启动文件、标准库文件。

动态链接使用动态链接库进行链接，生成的程序在执行的时候需要加载所需的动态库才能运行。动态链接生成的程序体积较小，但是必须依赖所需的动态库，否则无法执行。

静态链接使用静态库进行链接，生成的程序包含程序运行所需要的全部库，可以直接运行，不过静态链接生成的程序体积较大。

```jsx
gcc -c -o hello.o hello.c
gcc -o hello_shared hello.o
gcc -static -o hello_static hello.o
```

# Makefile

## 001_Makefile的引入及规则

使用keil, mdk, avr等工具开发程序时点点鼠标就可以编译了，
它的内部机制是什么？它怎么组织管理程序？怎么决定编译哪一个文件？

gcc -o test a.c b.c
// 简单,
// 但是会对所有文件都处理一次,
// 文件多时如果只修改其中一个文件会导致效率低

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/a00f1fb2971992d2db4254fe662915ec.png](D:\08 git\notion\all\韦东山\第一期\images\a00f1fb2971992d2db4254fe662915ec.png)

### Makefile的核心---规则 :

目标 : 依赖1 依赖2 ...
[TAB]命令

当"目标文件"不存在,
或
某个依赖文件比目标文件"新",
则: 执行"命令"

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/2688700f4e0d4bd7a3b517990532fb9f.png](D:\08 git\notion\all\韦东山\第一期\images\2688700f4e0d4bd7a3b517990532fb9f.png)

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/a5972fe1518976f0f65c89d78c06a81a.png](D:\08 git\notion\all\韦东山\第一期\images\a5972fe1518976f0f65c89d78c06a81a.png)

### 002_Makefile的语法

a. 通配符: %.o

$@ 表示目标
$< 表示第1个依赖文件
$^ 表示所有依赖文件

b. 假想目标: .PHONY

```makefile
test : a.o b.o c.o
	gcc -o test $^
%.o : %.c
	gcc -c -o $@ $<
c.o : c.c c.h
	gcc -c -o c.o c.c

clean:
	rm *.o test

.PHONY:
	clean
```

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/Untitled.png](D:\08 git\notion\all\韦东山\第一期\images\Untitled-1636555157193.png)

c. 即时变量、延时变量, export
简单变量(即时变量) :
A := xxx # A的值即刻确定，在定义时即确定
B = xxx # B的值使用到时才确定

:= # 即时变量
= # 延时变量
?= # 延时变量, 如果是第1次定义才起效, 如果在前面该变量已定义则忽略这句
+= # 附加, 它是即时变量还是延时变量取决于前面的定义

## 003_Makefile函数

a. $(foreach var,list,text)
b. $(filter pattern...,text)            # 在text中取出符合patten格式的值
     $(filter-out pattern...,text)    # 在text中取出不符合patten格式的值

c. $(wildcard pattern) # pattern定义了文件名的格式,      # wildcard取出其中存在的文件
d. $(patsubst pattern,replacement,$(var))         # 从列表中取出每一个值

                                                                                           # 如果符合pattern
                                                                                           # 则替换为replacement

A = a b c
B = $(foreach f,$(A),$(f).o)
C = a.o a.c b.o b.c
D = $(filter %.o,$(C))
E = $(filter-out %.o,$(C))
F = $(wildcard *.o) 
G = $(wildcard *.e)
H = $(patsubst %.o,%.c,$(F))

all:
	@echo B = $(B)
	@echo D = $(D)
	@echo E = $(E)
	@echo F = $(F)
	@echo G = $(G)
	@echo H = $(H)

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/Untitled%201.png](D:\08 git\notion\all\韦东山\第一期\images\Untitled 1-1636555164556.png)

参考文档:
a. 百度搜 "gnu make 于凤昌"
b. 官方文档: [http://www.gnu.org/software/make/manual/](http://www.gnu.org/software/make/manual/)

如果想深入, 可以学习这视频:
第3期视频项目1, 第1课第4节_数码相框_编写通用的Makefile_P

### 004_Makefile实例

a. 改进: 支持头文件依赖

[http://blog.csdn.net/qq1452008/article/details/50855810](http://blog.csdn.net/qq1452008/article/details/50855810)

gcc -M c.c // 打印出依赖

gcc -M -MF c.d c.c // 把依赖写入文件c.d

gcc -c -o c.o c.c -MD -MF c.d // 编译c.o, 把依赖写入文件c.d

b. 添加CFLAGS

```makefile
objs = a.o b.o c.o
dep_files := $(patsubst %,.%.d,$(objs))
dep_files := $(wildcard $(dep_files)) 

CFLAGS = -Werror -Iinclude 

test : $(objs)
	gcc -o test $^

ifneq ($(dep_files),)
include $(dep_files)
endif

%.o : %.c
	gcc $(CFLAGS) -c -o $@ $< -MD -MF .$@.d

clean:
	rm *.o test
disclean:
	rm $(dep_files)

.PHONY: clean
```

![%E7%AC%AC009%E8%AF%BE_gcc%E5%92%8Cmakefile%2069b6f9d9170840569a788ccc59469206/Untitled%202.png](D:\08 git\notion\all\韦东山\第一期\images\Untitled 2-1636555167428.png)

c. 分析裸板Makefile