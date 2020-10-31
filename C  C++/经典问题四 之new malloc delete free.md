- new malloc 区别
- delete free 区别
### new malloc 区别
- new是C++关键字
- malloc是C库提供函数
- new以具体类型为单位进行内存分配
- malloc以字节为单位进行内存分配
- new在申请内存空间时可以进行初始化（触发构造函数）
- malloc仅跟需要申请定量的内存空间
- 对象创建只能靠new
- malloc不适合面向对象的开发

```cpp
#include <iostream>
#include <string>
#include <cstdlib>

using namespace std;

class Test
{
    int* mp;
public:
    Test()
    {
        cout << "Test::Test()" << endl;
        mp = new int(100);//申请4b，初始化为100
		cout << *mp << endl;
    }
	~Test()
    {
		delete mp;
        cout << "~Test::Test()" << endl;
        
    }

};

int main()
{
    Test* pn = new Test;
    Test* pm = (Test*)malloc(sizeof(Test));//不合法空间
    
	free(pm);//会释放new申请空间，但不会调用析构函数
	delete(pn);
	
    
    return 0;
}

```
### delete free 区别
- delete 是C++关键字
- free 是C库提供函数，某些系统不能调用
- delete触发析构函数
- free仅归还之前分配的内存空间
- 对象销毁只能用delete
- free不适合面向对象的开发


