# 构造函数语义 （The Semantics of Constructors）

## 1. 默认构造函数（Default Constructor）的构造操作
### 例1：未定义默认构造函数
```C++
class Shy
{
public:
	Shy() : m_Degree(1) {}

private:
	int m_Degree;
};

class Person
{
public:

private:
	Shy      m_Shy;
	unsigned m_Age;
};

int main()
{
	Person Tom;

	return 0;
}
```
> VS2015: Person::m_Shy::m_Degree = 1, Person::m_Age = 3435973836

类**Person**没有提供默认构造函数，在执行`Person Tom;`代码段时，编译器将合成一个默认构造函数，类似于：
```C++
// 编译器合成默认构造函数
inline Person::Person()
{
	// 编译器附加上的Compiler Code
	m_Shy.Shy::Shy();
}
```
从编译器的行为可以得到如下结论：
* **如果类未显示地（explicit）定义默认构造函数（如类`Person`），编译器将生成一个合成默认构造函数**
* **合成默认构造函数只会附加初始化用户定义类型：`Shy`，不会附加初始化内置类型：`int`（定义类的默认构造函数时，最好显示初始化内置类型）**

### 例2：定义默认构造函数
```C++
class Shy
{
public:
	Shy() : m_Degree(1) {}

private:
	int m_Degree;
};

class Stupid
{ 
public: 
	Stupid() : m_Degree(2) {}

private:
	int m_Degree;
};

class Nervous
{
public:
	Nervous() : m_Degree(3) {}

private:
	int m_Degree;
};
 
class Person
{
public:
	Person() : m_Stupid(), m_Age(20)
	{

	}

private:
	Shy	 m_Shy;
	Stupid   m_Stupid;
	Nervous  m_Nervous;
	unsigned m_Age;
};

int main()
{
	Person Tom;

	return 0;
}
```
> VS2015: Person::m_Shy::m_Degree = 1, Person::m_Stupid::m_Degree = 2, Person::m_Nervous::m_Degree = 3, Person::m_Age = 20

类**Person**提供了默认构造函数，但该默认构造函数只显示的初始化了`m_Stupid`和`m_Age`。在执行`Person Tom;`代码段时，编译器行为类似于：
```C++
inline Person::Person()
{
	m_Shy.Shy::Shy();		// 编译器附加上的Compiler Code
	m_Stupid.Stupid::Stupid();	// 显示定义
	m_Nervous.Nervous::Nervous();	// 编译器附加上的Compiler Code
	m_Age = 20;			// 显示定义
}
```
虽然显示定义了默认构造函数，但编译器依然会**根据成员变量声明顺序**附加额外的代码，以保证所有成员变量都被初始化
