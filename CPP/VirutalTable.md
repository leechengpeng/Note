## C++虚函数表
C++虚函数表的作用是为了实现**多态**的机制。多态也是一种泛型技术：模板，RTTI，虚函数。
### 1. 虚函数表
定义一个基本的类`Shape`：
```C++
class Shape
{
public:
	virtual void area() { std::cout << "Shape Area" << std::endl; }
	virtual void circumference() { std::cout << "Shape circumference" << std::endl; }
};
```
首先定义一个指向对象`shape`本身的二维指针`pShape`，`pShape`指向对象本身所维护的成员指针表：
```C++
Shape shape;
int** pShape = reinterpret_cast<int**>(&shape);
```
对象`shape`所对应的虚函数表`pVirtualTable`为：
指针`pVirtualTable`可以定义为任意类型
