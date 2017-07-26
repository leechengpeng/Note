# 装饰者模式
> 动态的为对象添加一些额外的功能。

**装饰者模式**优点：
* 通过**组合**替换**继承**，使**装饰类**和**被装饰类**可独立发展，互相不耦合
* 可动态的给类扩展一些新的功能

**装饰者模式**缺点在于**多重装饰**导致整体可维护性变差，因此在使用的时候应尽量减少装饰类的数量。

## 实例
### 1. 通过继承添加功能
```C++
class Coffe
{
public:
	virtual void attribute();
};

class CoffeWithSugar : public Coffe
{
public:
	virtual void attribute() override;
};

class CoffeWithMilk : public Coffe
{
public:
	virtual void attribute() override;
};

class CoffeWithSugarAndMilk : public Coffe
{
public:
	virtual void attribute() override;
};
```
