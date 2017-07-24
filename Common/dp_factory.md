# 工厂模式
使用工厂模式有如下好处：
* **封装性**：一个对象只需要通过其类名即可完成实例化，不需要知道类的创建过程，降低了模块间的耦合
* **扩展性**：只需要在系统中添加新的产品类，不需要修改原有代码即可完成扩展

工厂模式是典型的**解耦框架**，高层模块只需要知道其产品的抽象类，不需要知道其产品类的具体实现。符合**迪米特法则**，不需要知道产品具体细节。符合**依赖倒置原则**，只依赖抽象，不依赖细节。符合**里氏替换原则**，使用具体产品替换虚拟产品。

## 1. 简单工厂/静态工厂
以女娲造人的传说为例子，抽象产品类：
```C++
class Human
{
public:
	virtual std::string color() = 0;
};
```
具体产品（人）：
```C++
class BlackMan : public Human 
{
public:
	virtual std::string color() override { return "Black"; }
};

class YellowMan : public Human
{
public:
	virtual std::string color() override { return "Yellow"; }
};
```
工厂（八卦炉）：
```C++
class HumanFactory
{
public:
	// 使用静态方法实现产品的创建。因此，简单工厂也叫做静态工厂
	static Human* createHuman(const std::string& vHumanType)
	{
		Human* pHuman = nullptr;

		if (vHumanType == "BlackMan")
		{
			pHuman = new BlackMan();
		}
		else if (vHumanType == "YellowMan")
		{
			pHuman = new YellowMan();
		}
		else
		{
			std::cerr << "Creating error, unknown huaman type '" << vHumanType <<"'" << std::endl;
		}

		return pHuman;
	}
};
```
生产具体产品（女娲）：
```C++
void nvwa()
{
	std::string HumanType = "YellowMan";
	Human* pHuman = HumanFactory::createHuman(HumanType);

	if (pHuman)
	{
		std::cout << "The human features is: " << pHuman->color() << std::endl;
	}
}
```
**简单工厂**不符合**开闭原则**，当增加白种人的时候，需要在`HumanFactory`里面增加白种人的判断逻辑。因此，**简单工厂**实际上不是一种设计模型，很少用在实际项目中。

## 2. 工厂方法模式
**工厂方法模式的每个产品都对应一个生成该产品的工厂**，而不是通过一个工厂来生成所有产品。在此不讲解详细流程，类似于如下代码：
```C++
class BlackManFactory : public AbstrctHumanFactory
{
};
...
void nvwa()
{
	// 黑种人的工厂
	Human* pBlackHuman  = BlackManFactory::createHuman();
	// 黄种人的工厂
	Human* pYellowHuman = YellowManFactory::createHuman();
	
	// 添加新的人种，只需在客户端添加如下代码即可：
	Human* pWhiteHuman  = WhiteManFactory::createHuman();
}
```
**工厂方法模式**将**变化因素**转移到客户端（女娲），在一定程度上解决**简单工厂**的**开闭问题**。

## 3. 抽象工厂模式
之前的女娲造人只创建了人类的颜色，人类的性别却别有区分。我们将性别加入变化当中，这个时候需要创建产品的影响因素其实是二维的。可以通过**抽象工厂模式**创建两个不同的具体工厂来满足创建需求。

抽象产品（人类），添加了一个性别项：
```C++
class Human
{
public:
	virtual std::string color() = 0;
	virtual std::string gender() = 0;
};

class BlackMan : public Human 
{
public:
	virtual std::string color() override { return "Black"; }
};

class YellowMan : public Human
{
public:
	virtual std::string color() override { return "Yellow"; }
};
```
具体产品：
```C++
class FemaleBlackMan : public BlackMan
{
public:
	virtual std::string gender() override { return "Female"; }
};

class MaleBlackMan : public BlackMan
{
public:
	virtual std::string gender() override { return "Male"; }
};

class FemaleYellowMan : public YellowMan
{
public:
	virtual std::string gender() override { return "Female"; }
};

class MaleYellowMan : public YellowMan
{
public:
	virtual std::string gender() override { return "Male"; }
};
```
工厂：
```C++
class FemaleHumanFactory : public HumanFactory
{
public:
	virtual Human* createHuman(const std::string& vHumanType) override
	{
		Human* pHuman = nullptr;

		if (vHumanType == "BlackMan")
		{
			pHuman = new FemaleBlackMan();
		}
		else if (vHumanType == "YellowMan")
		{
			pHuman = new FemaleYellowMan();
		}
		else
		{
			std::cerr << "Creating error, unknown huaman type '" << vHumanType << "'" << std::endl;
		}

		return pHuman;
	}
};
// 篇幅有限，不在赘述创建男性的工厂
```
客户端：
```C++
void nvwa()
{
	HumanFactory* pHumanFactory = new FemaleHumanFactory();

	std::string HumanType = "YellowMan";
	Human* pHuman = pHumanFactory->createHuman(HumanType);

	if (pHuman)
	{
		std::cout << "The human features is: " << pHuman->color() << "&" << pHuman->gender() << std::endl;
	}
}
```
