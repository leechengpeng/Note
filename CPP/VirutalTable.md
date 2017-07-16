## C++虚函数表
C++虚函数表的作用是为了实现**多态**的机制。多态也是一种泛型技术：模板，RTTI，虚函数。
### 1. 虚函数表
定义一个基类`Shape`：
```C++
class Shape
{
public:
	virtual void area() { std::cout << "Shape Area" << std::endl; }
	virtual void circumference() { std::cout << "Shape circumference" << std::endl; }
};
```
对象`shape`所对应的虚函数表`pVirtualTable`为：
```C++
Shape shape;
int* pVirtualTable = *reinterpret_cast<int**>(&shape);
```
从虚函数表`pVirtualTable`中取到其存放的虚函数指针：
```C++
typedef void(*pFun)();

pFun pArea = reinterpret_cast<pFun>(*(pVirtualTable + 0));
pFun pCircumference = reinterpret_cast<pFun>(*(pVirtualTable + 1));

pArea();
pCircumference();
```
VS2013运行结果为：
> Shape Area

> Shape circumference

### 2. 无覆盖继承
```C++
class Triangle : public Shape
{
public:
	virtual void description() { std::cout << "This is a triangle" << std::endl; }
};
```
```C++
Triangle triangle;
int* pVirtualTable = *reinterpret_cast<int**>(&triangle);

pFun pArea = reinterpret_cast<pFun>(*(pVirtualTable + 0));
pFun pCircumference = reinterpret_cast<pFun>(*(pVirtualTable + 1));
pFun pDescription = reinterpret_cast<pFun>(*(pVirtualTable + 2));

pArea();
pCircumference();
pDescription();
```

VS2013运行结果为：
> Shape Area

> Shape circumference

> This is a triangle

由上述代码可知无覆盖继承有以下特点：
1. 虚函数按照其**声明顺序**存放于表中
2. 父类的虚函数在子类的虚函数前面
