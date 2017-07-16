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
