## C++类的大小
### 1. 空类的大小
定义一个空的类`Animal`：
```C++
class Animal {};
std::cout << sizeof(Animal) << std::endl; // VS2012: 1
```
空类`Animal`隐藏1byte大小的空间，因为编译器强制的给类`Animal`插入了一个**char**型数据，使类`Animal`的每个对象在内存中都有自己唯一的地址。

### 2. 带有虚函数的类
```C++
class CPoint3d 
{
public:
	virtual ~CPoint3d() {}
  
	virtual unsigned length() const {}
};

std::cout << sizeof(CPoint3d) << std::endl; // VS2015：8
```
带有虚函数的类的大小为8字节：**类自带的1byte字节对齐** + **指向虚函数表的指针（4 byte）**。

### 3. 继承类
```C++
class CPoint4d : public CPoint3d
{
public:
	virtual ~CPoint4d() {}

	virtual unsigned length() const override {}
	virtual unsigned size() const {}
};

std::cout << sizeof(CPoint4d) << std::endl; // VS2015：8
```
继承依然大小为8byte，原理同2（详情参见[虚函数表](VirutalTable.md)）。

### 4. 多重继承
```C++
class CDesription
{
public:
	virtual void description() const {}
};

class CPoint4d : public CPoint3d, public CDesription
{
public:
	virtual ~CPoint4d() {}

	virtual unsigned length() const override {}
	virtual unsigned size() const {}
};

std::cout << sizeof(CPoint4d) << std::endl; // VS2015：16
```
此时，类`CPoint4d`需要维护两张虚函数表，再按字节对齐，类的大小即为16byte。

### 5. [虚继承](VirtualExtends.md)
```C++
class Animal { };
class Tiger : public virtual Animal { };
class Lion  : public virtual Animal { };
```
```C++
std::cout << sizeof(Animal) << std::endl; // VS2012: 1
std::cout << sizeof(Tiger) << std::endl;  // VS2012: 4
std::cout << sizeof(Lion) << std::endl;   // VS2012: 4
```
子类会被赋予一个指向**虚基类**的指针（最新的编译器设计会把空类本身的一个字节大小抹去，由该指针表示类本身）。
```C++
class Liger : public Tiger, public Lion {};

std::cout << sizeof(Liger) << std::endl; // VS2012: 8
```
类`Liger`里面存放了类`Tiger`和`Lion`两个类的实例，因次大小为8。
