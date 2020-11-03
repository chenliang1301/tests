## try catch（捕捉异常）
- try 正常代码
- catch处理（try语句的）异常情况
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629083024238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
## thow抛出异常
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629083203327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

# C++异常处理
- throw抛出异常必须catch处理
	- 当前函数能处理异常，继续
	- 否则，函数停止 执行，并返回异常，无返回值

# 未被处理的异常会顺着函数调用栈向上传播直到被处理为止，否则程序停止执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629083717969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
## 示例

```cpp
#include <iostream>
#include <string>
#include <csetjmp>

using namespace std;

double divide(double a, double b)
{
    const double delta = 0.000000000000001;
    double ret = 0;
    
    if( !((-delta < b) && (b < delta)) )
    {
        ret = a / b;
    }
    else
    {
        throw 0;
    }
    
    return ret;
}

int main(int argc, char *argv[])
{   
	try
	{
		double r = divide(1, 0);
		cout << "r = " << r << endl;
	}
	catch(...)//...
	{
		cout << "Divided by zero..." << endl;
	}
	

    return 0;
}


```


## 同一个try 语句可以跟上多个catch语句
- catch（...）捕获任何异常
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629084630909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

# 异常处理匹配原则
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020062908473184.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

## 示例

```cpp
#include <iostream>
#include <string>

using namespace std;
void Demo1()
{
	try
	{
		throw 1;
	}
	catch(char c)
	{
		cout << "catch(char c)" << endl;
	}
	catch(short c)
	{
		cout << "catch(short c)" << endl;
	}
	catch(double c)
	{
		cout << "catch(double c)" << endl;
	}
	catch(...)
	{
		cout << "catch(...)" << endl;
	}	
}

void Demo2()
{
	throw string("che");
}


int main(int argc, char *argv[])
{    
    Demo1();
    
	try
	{
		Demo2();
	}
	catch(char* s)
	{
		cout << "catch(char* s)" << endl;
	}
	catch(const char* cs)
	{
		cout << "catch(const char* cs)" << endl;
	}	
	catch(string ss)
	{
		cout << "catch(string ss)" << endl;
	}
    
    return 0;
}


```
# 小结
- 异常处理必须严格匹配，不会进行任何类型的转换
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629085708934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
