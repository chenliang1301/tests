## 基类指针指向子类对象
## 基类引用成为子类对象的别名
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629104856994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

# 静态类型 - 变量（对象）自身的类型
# 动态类型-指针（引用）所指向对象的实际类型
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629105858352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

##  解决 -利用多态
- 1.基类中定义虚函数返回具体类型信息
- 2.所有派生类都必须实现类型相关的虚函数
- 3.没个类的类型虚函数都需要不同的实现

## 示例1
```cpp
#include <iostream>
#include <string>

using namespace std;

//fulei jilei
class Base
{
public:
	virtual string type()
	{
		return "Base";
	}
};

class Derived : public Base
{
public:
	string type()
	{
		return "Derived";
	}
	void printf()
	{
		cout << "I'am a Derived" << endl;
	}
	
};

class Child : public Base
{
public:
	string type()
	{
		return "Child";
	}
};

void Test(Base* b)
{
	/* dangerious */
	//Derived* d = static_cast<Derived*>(d);
	cout << b->type() << endl;
	if(b->type() == "Derived")
	{
		Derived* d = static_cast<Derived*>(b);
		
		d->printf();
	}
	
	//cout << dynamic_cast<Derived*>(b) << endl; //继承关系才能正确转换
}

int main(int argc, char *argv[])
{
	Base b;
	Derived d;
	Child c;
	
	Test(&b);
	Test(&d);
	Test(&c);
    
    return 0;
}


```

## 多态缺陷
- 必须从基类开始提供类型虚函数
- 所有派生类都必须重写类型虚函数
- 每个派生类的类型名必须唯一

## C++提供typeid关键字获取类型信息
- typeid关键字返回对应参数的类型信息
- typeid返回一个type_info乐力对象
- 当typeid的参数为NULL时将抛出异常

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629113301638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629113420500.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE3Mzk0OA==,size_16,color_FFFFFF,t_70)

## 示例二

```cpp
#include <iostream>
#include <string>
#include <typeinfo>

using namespace std;

//fulei jilei
class Base
{
public:
	virtual ~Base()
	{
		
	}
};

class Derived : public Base
{
public:
	void printf()
	{
		cout << "I'am a Derived" << endl;
	}
	
};


void Test(Base* b)//没有虚函数表，则为静态类型，父类类型
{
	const type_info& tb = typeid(*b);
	cout << tb.name() << endl;
}

int main(int argc, char *argv[])
{
	int i=0;
	const type_info& tiv = typeid(i);
	const type_info& tii = typeid(int);
	
	cout << (tiv==tii) << endl;
	
	Base b;
	Derived d;
	
	Test(&b);
	Test(&d);
    
    return 0;
}


```

## 小结
- C++中有静态类型和动态类型
- 利用多态能够实现对象的动态类型识别
- typeid是专用类型识别的关键字
- typeid能够返回对象的动态信息
