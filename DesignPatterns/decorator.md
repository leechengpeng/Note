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
### 2. 通过装饰者模式添加功能
```C++
class Coffe
{
public:
	virtual void attribute()
	{

	}
};

// 装饰类：Sugar
class Sugar : public Coffe
{
public:
	Sugar(Coffe* vCoffe) : m_Coffe(vCoffe) {}

	virtual void attribute() override
	{
		m_Coffe->attribute();

		// 添加新功能
		std::cout << "加糖 " << std::endl;
	}

private:
	Coffe* m_Coffe;
};

// 装饰类：Milk
class Milk : public Coffe
{
public:
	Milk(Coffe* vCoffe) : m_Coffe(vCoffe) {}

	virtual void attribute() override
	{
		m_Coffe->attribute();

		// 添加新功能
		std::cout << "加牛奶 " << std::endl;
	}

private:
	Coffe* m_Coffe;
};

void client()
{
	Coffe* pCoffe = new Coffe();

	Coffe* pCoffeWithSugar = new Sugar(pCoffe);
	Coffe* pCoffeWithMilk  = new Milk(pCoffe);

	pCoffeWithSugar->attribute();
	pCoffeWithMilk->attribute();

	Coffe* pCoffeWithSugarAndMilk = new Milk(pCoffeWithSugar);
	pCoffeWithSugarAndMilk->attribute();
}
```
### 3. 继承 VS 装饰者模式
通过对比**继承**和**装饰者模式**不同的添加新功能的方式可知：
* 继承需要将不同的新功能组合`Milk`、`Sugar`、`Milk + Sugar`，会使子类异常的多，灵活性差
* 装饰者模式通过客户端将需要的功能自行组装，装饰类只需完成自己单一的装饰即可，增加了设计的灵活性
