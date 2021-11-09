![在这里插入图片描述](https://img-blog.csdnimg.cn/20200628210948433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200628211001510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200628211123776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
## C异常处理

```cpp
#include <iostream>
#include <string>

using namespace std;

double divide(double a, double b, int* valid)
{
    const double delta = 0.000000000000001;
    double ret = 0;
    
    if( !((-delta < b) && (b < delta)) )
    {
        ret = a / b;
        
        *valid = 1;
    }
    else
    {
        *valid = 0;
    }
    
    return ret;
}

int main(int argc, char *argv[])
{   
    int valid = 0;
    double r = divide(1, 0, &valid);
    
    if( valid )
    {
        cout << "r = " << r << endl;
    }
    else
    {
        cout << "Divided by zero..." << endl;
    }
    
    return 0;
}


```
## divide函数有三个函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020062821324730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
```cpp
#include <iostream>
#include <string>
#include <csetjmp>

using namespace std;

static jmp_buf env;//程序执行的上下文

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
        longjmp(env, 1);
    }
    
    return ret;
}

int main(int argc, char *argv[])
{   
    if( setjmp(env) == 0 )
    {
        double r = divide(1, 0);
        
        cout << "r = " << r << endl;
    }
    else
    {
        cout << "Divided by zero..." << endl;
    }
    
    return 0;
}


```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020062821333714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200628213420203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

## 小结
![- 异常是在开发中可预料的](https://img-blog.csdnimg.cn/20200628213637361.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
