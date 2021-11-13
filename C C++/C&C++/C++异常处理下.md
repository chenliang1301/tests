## catch语句块可以抛出异常
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629090023916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

## catch中捕获异常可以被重新解释后抛出
- 为了统一异常类型
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629090308953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

## 示例

```cpp
#include <iostream>
#include <string>

using namespace std;

void Demo()
{
		try
		{
		try
		{
			throw 0;
		}
		catch(int i)
		{
			cout << "catch(int i)" << endl;
			throw i;
		}
		catch(...)
		{
			cout << "catch(...) in" << endl;
			throw;
		}
	}
	catch(...)
	{
		cout << "catch(...) out" << endl;
	}
}

/* 
	void func(int i)
	异常int
	-1 ==》参数
	-2 ==》运行
	-3 ==》超时
 */
 
void func(int i)
{
	if( i<0 )
	{
		throw -1;
	}
	if( i>100 )
	{
		throw -2;
	}
	if( i==11 )
	{
		throw -3;
	}
	cout << "run func" << endl;
}

void Myfunc(int i)
{
	try
	{
		func(i);
	}
	catch(int i)
	{
		switch(i)
		{
			case -1:
				throw "invalid Paramter";
				break;
			case -2:
				throw "Run Exception";
				break;
			case -3:
				throw "Timeout Exception";				
				break;
		}
	}
}
 

int main(int argc, char *argv[])
{
	try
	{
		Myfunc(11);
	}
	catch (const char* cs)
	{
		cout << "Exception Info:" << cs << endl;
	}
    
    return 0;
}
```
## 异常处理规则
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629091854590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629091939871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)
## 引用作为参数，避开拷贝构造提供程序的效率

```cpp
#include <iostream>
#include <string>

using namespace std;

class Base
{
	
};

class Exception : public Base
{
	int m_id;
	string m_desc;
public:
	Exception(int id, string desc)
	{
		m_id = id;
		m_desc = desc;
	}
	int id() const
	{
		return m_id;
	}
	string description() const
	{
		return m_desc;
	}
};

/* 
	void func(int i)
	异常int
	-1 ==》参数
	-2 ==》运行
	-3 ==》超时
 */
 
void func(int i)
{
	if( i<0 )
	{
		throw -1;
	}
	if( i>100 )
	{
		throw -2;
	}
	if( i==11 )
	{
		throw -3;
	}
	cout << "run func" << endl;
}

void Myfunc(int i)
{
	try
	{
		func(i);
	}
	catch(int i)
	{
		switch(i)
		{
			case -1:
				throw Exception(-1, "invalid Paramter");
				break;
			case -2:
				throw Exception(-2, "Run Exception");
				break;
			case -3:
				throw Exception(-3, "Timeout Exception");				
				break;
		}
	}
}
 

int main(int argc, char *argv[])
{
	try
	{
		Myfunc(11);
	}
	catch (const Exception& e)//防止拷贝构造？？？, const对象
	{
		cout << "Exception Info:" << endl;
		cout << "	ID:" << e.id() <<endl;
		cout << "	Description: " << e.description() << endl;
		
	}
	catch(const Base& b)//父类异常要放在子类异常下面，否则将无法进入子类异常，（子类异常必然属于父类异常，赋值兼容性原则）
	{
		cout << "catch(const Base& b): " << endl;
	}
	
    
    return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629093308510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629093344526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

```cpp

#ifndef _ARRAY_H_
#define _ARRAY_H_
#include <exception>

using namespace std;

template
< typename T, int N >
class Array
{
    T m_array[N];
public:
    int length() const;
    bool set(int index, T value);
    bool get(int index, T& value);
    T& operator[] (int index);
    T operator[] (int index) const;
    virtual ~Array();
};

template
< typename T, int N >
int Array<T, N>::length() const
{
    return N;
}

template
< typename T, int N >
bool Array<T, N>::set(int index, T value)
{
    bool ret = (0 <= index) && (index < N);
    
    if( ret )
    {
        m_array[index] = value;
    }
    
    return ret;
}

template
< typename T, int N >
bool Array<T, N>::get(int index, T& value)
{
    bool ret = (0 <= index) && (index < N);
    
    if( ret )
    {
        value = m_array[index];
    }
    
    return ret;
}

template
< typename T, int N >
T& Array<T, N>::operator[] (int index)
{
    if( (0 <= index) && (index < N) )
    {
        return m_array[index];
    }
	else
	{
		throw out_of_range("T& Array<T, N>::operator[] (int index)");
	}
}

template
< typename T, int N >
T Array<T, N>::operator[] (int index) const
{
    if( (0 <= index) && (index < N) )
    {
        return m_array[index];
    }
	else
	{
		throw out_of_range("T Array<T, N>::operator[] (int index) const");
	}
}

template
< typename T, int N >
Array<T, N>::~Array()
{

}

#endif
```

```cpp
#ifndef _HEAPARRAY_H_
#define _HEAPARRAY_H_

#include <stdexcept>

using namespace std;

template
< typename T >
class HeapArray
{
private:
    int m_length;
    T* m_pointer;
    
    HeapArray(int len);
    HeapArray(const HeapArray<T>& obj);
    bool construct();
public:
    static HeapArray<T>* NewInstance(int length); 
    int length() const;
    bool get(int index, T& value);
    bool set(int index ,T value);
    T& operator [] (int index);
    T operator [] (int index) const;
    HeapArray<T>& self();
    const HeapArray<T>& self() const;
    ~HeapArray();
};

template
< typename T >
HeapArray<T>::HeapArray(int len)
{
    m_length = len;
}

template
< typename T >
bool HeapArray<T>::construct()
{   
    m_pointer = new T[m_length];
    
    return m_pointer != NULL;
}

template
< typename T >
HeapArray<T>* HeapArray<T>::NewInstance(int length) 
{
    HeapArray<T>* ret = new HeapArray<T>(length);
    
    if( !(ret && ret->construct()) ) 
    {
        delete ret;
        ret = 0;
    }
        
    return ret;
}

template
< typename T >
int HeapArray<T>::length() const
{
    return m_length;
}

template
< typename T >
bool HeapArray<T>::get(int index, T& value)
{
    bool ret = (0 <= index) && (index < length());
    
    if( ret )
    {
        value = m_pointer[index];
    }
    
    return ret;
}

template
< typename T >
bool HeapArray<T>::set(int index, T value)
{
    bool ret = (0 <= index) && (index < length());
    
    if( ret )
    {
        m_pointer[index] = value;
    }
    
    return ret;
}

template
< typename T >
T& HeapArray<T>::operator [] (int index)
{
	bool ret = (0 <= index) && (index < length());
	if(ret) 
	{
		return m_pointer[index];
	}
	else
	{
		throw out_of_range("T& HeapArray<T>::operator [] (int index)");
	}
}

template
< typename T >
T HeapArray<T>::operator [] (int index) const
{
    if( (0 <= index) && (index < length()) )
    {
        return m_pointer[index];
    }
    else
    {
        throw out_of_range("T HeapArray<T>::operator [] (int index) const");
    }
}

template
< typename T >
HeapArray<T>& HeapArray<T>::self()
{
    return *this;
}

template
< typename T >
const HeapArray<T>& HeapArray<T>::self() const
{
    return *this;
}

template
< typename T >
HeapArray<T>::~HeapArray()
{
    delete[]m_pointer;
}


#endif

```

```cpp
#include <iostream>
#include <string>
#include "Array.h"
#include "HeapArray.h"

void TestArray()
{
	Array<int, 5> a;
	
	for(int i=0; i<a.length(); i++)
	{
		a[i] = i;
	}
	
	for(int i=0; i<a.length(); i++)
	{
		cout << a[i] << endl;
	}
}

void TestHeapArray()
{
	HeapArray<double>* pa = HeapArray<double>::NewInstance(5);//二阶构造
	
	if(pa!= NULL)
	{
		HeapArray<double>& array = pa->self();
		for(int i=0; i<array.length(); i++)
		{
			array[i] = i;
		}
		
		for(int i=0; i<array.length(); i++)
		{
			cout << array[i] << endl;
		}
	}
	delete pa;//不用delete则需要智能指针
}

int main(int argc, char *argv[])
{
    

	try
	{
		TestArray();
		cout << endl;
		TestHeapArray();
	}
	catch(...)
	{
		cout << "Execption" << endl;
	}
    return 0;
}


```
## 小结
- catch语句野可以抛出异常
- 异常类型可以自定义类型（子类上父类下）
- 赋值兼容性原则在异常匹配中依然适用
- 标准库异常从exception类派生的


