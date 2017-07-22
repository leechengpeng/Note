## C++类的大小
### 1. 空类的大小
定义一个空的类`Animal`：
```C++
class Animal {};
std::cout << sizeof(Animal) << std::endl; // VS2012: 1
```
空类`Animal`隐藏1 byte大小的空间，因为编译器强制的给类`Animal`插入了一个**char**型数据，使类`Animal`的每个对象在内存中都有自己唯一的地址。

### 2. 带有虚函数的类
```C++
class Animal 
{
public:
	virtual ~Animal();

	virtual unsigned wightV();
};

std::cout << sizeof(Animal) << std::endl;; // VS2012：4
```
因为**虚函数**的存在，因此类本身存放了一个指向**虚函数表**的指针。
> **新的编译器做了特殊优化，将类本身的成员变量（此处为指向虚函数表的指针）作为类唯一的地址，去掉了编译器安插的那个char，避免了因字节对齐而使类的大小增加。**

### 3. 继承类
```C++
class Tiger : public Animal 
{
public:
	virtual unsigned wightV() override {}
	virtual unsigned typeV() {}
};

std::cout << sizeof(Tiger) << std::endl;; // VS2012：4
```
继承类大小依然为4 byte，原理同2，详情参见[虚函数表](VirutalTable.md)。

### 4. 多重继承
```C++
class Desription
{
public:
	virtual void description() const {}
};

class Tiger : public Animal, public Desription
{
public:
	virtual unsigned wightV() override {}
	virtual unsigned typeV() {}
};

std::cout << sizeof(Tiger) << std::endl;; // VS2012：8
```
此时，类`Tiger`需要维护两张虚函数表，类的大小即为8 byte。

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
类`Liger`里面存放了类`Tiger`和`Lion`两个类的实例，因此大小为8 byte。
