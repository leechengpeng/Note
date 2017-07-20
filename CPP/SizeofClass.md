## C++类的大小
### 1. 空类的大小
定义一个空的类`CPoint3d`：
```C++
class CPoint3d {};

std::cout << sizeof(CPoint3d) << std::endl;  // VS2015: 1
```
空类`CPoint3d`中虽然没有定义任何数据，但编译器会强制的给类`CPoint3d`插入一个**char**（1 byte）型的数据。这种策略使得类`CPoint3d`的对象在内存中都有自己独立的地址。
```C++
CPoint3d Point1;
CPoint3d Point2;

std::cout << "Point1: " << &Point1 << std::endl;  // VS2015: 0x0058f833
std::cout << "Point2: " << &Point2 << std::endl;  // VS2015: 0x0058f827
```

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