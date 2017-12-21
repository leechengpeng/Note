# C++对象模型（C++ Object Model）
## 1. 对象模型的设计
C++中有以下几种成员（Member）类型：
> * 成员变量类型：静态成员变量（Static）和非静态成员变量（Nonstatic）
> * 成员函数类型：静态成员函数（Static）、非静态成员函数（Nonstatic）和虚函数（Virtual）

考虑一维点的类型设计：
```C++
class CPoint
{
public:
	CPoint(int vX = 0);
	virtual ~CPoint();  		// 虚函数

	float x() const;  		// 成员函数（非静态成员函数）
	static unsigned getNumsPoint(); // 静态成员函数
	
private:
	float m_X;  			// 成员变量（非静态成员变量）
	static unsigned m_NumsPoint;  	// 静态成员变量
};
```
类`CPoint`中拥有了所有C++类可能的成员类型，如何设计其对象模型才能降低类带来的管理成本（相对于C）？

### 1.1. 简单对象模型
简单对象模型设计十分简单，一个对象存放一系列Slots，每个Slot指向一个类成员（按照类成员声明顺序）。简单对象模型能有效的降低C++编译器的设计复杂度，但其在空间和执行期的效率却相对较低。

![简单对象模型](https://github.com/leechengpeng/Note/blob/master/Resources/Images/%E7%AE%80%E5%8D%95%E5%AF%B9%E8%B1%A1%E6%A8%A1%E5%9E%8B.png)

### 1.2. 表格驱动对象模型
基于表格驱动对象模型是把类成员根据其类型的不同划分为两个表：
> * **成员变量表（Data Member Table）**：直接保存成员变量
> * **成员函数表（Function Member Table）**：类似于简单对象模型，保存指向成员函数的指针

**类对象（Class Object）**则直接保存指向这两个表的指针。

![表格驱动模型](https://github.com/leechengpeng/Note/blob/master/Resources/Images/%E8%A1%A8%E6%A0%BC%E9%A9%B1%E5%8A%A8%E6%A8%A1%E5%9E%8B.png)

### 1.3. C++对象模型
C++对象模型是由**简单对象模型**派生而来，并且对**内存空间**和**存取时间**做了相关优化。C++对象模型设计如下：
> * **成员变量**：直接存放到**类对象**中
> * **静态成员变量**：存放到**类对象**之外
> * **成员函数和静态成员函数**：存放到**类对象**之外
> * **虚函数**：
> 	1. 每个类（class）维护一个虚函数表（Virtual Function Table，VFT）。VFT的第一个slot存放这个类的**type_info**，后续slot分别存放类所对应的虚函数
> 	2. 每个**类对象**存放一个**vptr**指向VFT（**vptr**的设定和重置是由类的构造函数、析构函数和复制拷贝预算符控制）

![C++对象模型](https://github.com/leechengpeng/Note/blob/master/Resources/Images/C%2B%2B%E5%AF%B9%E8%B1%A1%E6%A8%A1%E5%9E%8B.png)

**C++对象模型**的主要优点在于它的空间和存取效率比较高。主要缺点则是：如果应用程序本身没有变动，而是其所用到的类对象的非静态成员变量变动（增加、删除或更改），那么该应用程序就必须重新编译。

## 2. 添加继承的对象模型
### 2.1. 简单对象模型的继承
直接在slot中添加一个slot指向基类的实例，优点在于对象的大小不会受到基类的影响。
### 2.2. 表格驱动对象模型的继承
在类对象中添加一个指向基类表（Base Class Table）的指针，优点同简单对象模型。
### 2.3. C++对象模型的继承
以上两种模型的继承模型都采用间接性的方法，C++对象模型不运用任何间接性，而是直接将基类的数据成员存放到类对象中。优点是对数据最有效的存取，缺点在于基类的任意变更，都会导致重新编译。
## 3. 对象模型对程序的影响
不同的对象模型，可能会导致两个不同的结果：
1. 现有程序代码必须修改
2. 必须加入新的程序代码
```C++
X fooBar()
{
	X x;
	x.foo();
	
	X* pX = new X();
	pX->foo();
	delete pX;
	
	return x;
}
```
其中类X中的`foo()`是虚函数，`fooBar()`函数内部可能被转化为：
```C++
// 虚拟C++代码
void fooBar(X& voResult)
{
	// 用voResult替代局部对象x
	// 构造voResult	
	voResult.X::X();
	// 扩展x.foo()，不使用virtual机制
	// C++调用类成员函数的时候，会向成员函数传入指向类对象的指针，方便成员函数对类对象变量的访问（不同类对象拥有不同的类成员变量值）
	foo(&voResult);		
	
	// 扩展new pX
	pX = _new(sizeof(X));
	if (pX) pX->X::X();
	
	// 使用virtual机制扩展pX->foo()
	(*pX->vtbl[2])(pX);
	
	// 扩展delete pX
	// 注意此处对指针的判断语句，C++在设计的时候都已经添加对指针是否为空的判断，因此在使用delete的时候不需要判断指针是否为空
	if (pX)
	{
		// 调用析构函数（析构函数是虚函数，因此也需要virtual机制）
		(*pX->vtbl[1])(pX);
		_delete(pX);
	}
	
	// 无需返回局部对象x
	return;
}
```
