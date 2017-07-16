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
Shape* pTriangle = new Triangle();
int* pVirtualTable = *reinterpret_cast<int**>(&(*pTriangle));

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

### 3. 有覆盖继承
```C++
class Rectangle : public Shape
{
public:
	virtual void circumference() override { std::cout << "Rectangle circumference" << std::endl; }
	virtual void description() { std::cout << "This is a rectangle" << std::endl; }
};
```
```C++
Shape* pRectangle = new Rectangle();
int* pVirtualTable = *reinterpret_cast<int**>(&(*pRectangle));

pFun pArea = reinterpret_cast<pFun>(*(pVirtualTable + 0));
pFun pCircumference = reinterpret_cast<pFun>(*(pVirtualTable + 1));
pFun pDescription = reinterpret_cast<pFun>(*(pVirtualTable + 2));

pArea();
pCircumference();
pDescription();
```

VS2013运行结果为：
> Shape Area

> Rectangle circumference

> This is a triangle

由上述代码可知有覆盖继承有以下特点：
1. 覆盖虚函数会覆盖原有父类的虚函数

通过有覆盖继承，我们就可以看到类似于下面的代码：
```C++
Base* pDerived = new Derive();
pDerived->Fun(); // Fun被Derive类覆盖
```
由`pDerived`指向的虚函数表中`Fun()`的位置已经被`Derive::Fun()`函数地址所取代，实际调用时也就调用了子类对应的`Fun`函数。这样就实现了多态。

### 4. 多重继承
由上述实例，基本的原理基本都已经讲解清楚，再次不再赘述多重继承的代码。多重继承的有覆盖和无覆盖有以下特点：
1. 子类一次存放多个父类的虚函数表
2. 子类成员函数被放到第一个父类的虚函数表中
3. 子类覆盖函数会替换其对应的父类对应虚函数
