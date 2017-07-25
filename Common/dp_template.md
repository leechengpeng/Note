# 模板方法模式（Template Method Pattern）
**模板方法模式**是基于代码复用的基本技术，其定义如下：
> 基类中定义一个**模板方法**，**模板方法**调用了一系列**基本方法**（子类负责实现），完成一组固定逻辑
```C++
class AbstractClass
{
public:
	void templateMethod()
	{
		/*
		// 一些其他的代码逻辑
		*/

		method1();
		method2();
	}

protected:
	virtual void method1();
	virtual void method2();
};
```
通过**模板方法模式**的示例代码可知：
* 封装了不变的部分，扩展了可变部分
* 提取公共部分代码，便于维护
* 行为由父类控制，子类实现

增加代码逻辑的时候，只需要改变模板方法模式的调用而不影响接口的设计：
```C++
class AbstractClass
{
public:
	void templateMethod()
	{
		/*
		// 一些其他的代码逻辑
		*/

		method1();
		method2();
		method3();
	}

protected:
	virtual void method1();
	virtual void method2();
	virtual void method3();
};
```
