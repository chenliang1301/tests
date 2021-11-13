# 第23课-#error和#line使用分析

```c
//23-2.c
#include <stdio.h>
 
void f(){
    #if(PRODUCT == 1)
        printf("this is high lever!\n");
    #elif(PRODUCT == 2)   
        printf("this is middle lever!\n");
    #else 
        #error printf("the "PRODUCT" is not define\n");
    #endif
}

int main(){

    f();
    printf("1.funtion look\n");
    printf("2.funtion walk\n");      
    printf("3.funtion speak\n");         

    #if(PRODUCT == 1)   
        printf("4.funtion look\n");
        printf("5.funtion walk\n"); 
        printf("6.exit\n"); 
    #elif(PRODUCT == 2)  
        printf("4.funtion look\n");
        printf("5.exit\n"); 
    #else 
        #error printf("the "PRODUCT" is not define\n");
    #endif
  
    return 0;
}

//a.out
23-2.c: In function ‘f’:
23-2.c:10:10: error: #error printf("the "PRODUCT" is not define\n");
         #error printf("the "PRODUCT" is not define\n");
          ^
23-2.c: In function ‘main’:
23-2.c:29:10: error: #error printf("the "PRODUCT" is not define\n");
         #error printf("the "PRODUCT" is not define\n");

总结：
		1、#error 用于自定义编译错误信息
		2、#warning 用于自定义警告错误信息 
		3、常用于条件编译
		4、#line 强制行号，文件名

```