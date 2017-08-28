# 构造函数语义 （The Semantics of Constructors）

## 1. 默认构造函数（Default Constructor）的构造操作
### 例1：未定义默认构造函数
```C++
class Person
{
private:
	unsigned    m_Age;
	std::string m_Name;
};

int main()
{
	Person Tom;

	return 0;
}
```
```
Tom::m_Age  = 3435973836;
Tom::m_Name = "";
```
类**Person**没有显示定义构造函数，编译器会为其提供一个**合成默认构造函数**：
```C++
// 编译器合成默认构造函数
inline Person::Person()
{
	// 编译器附加上的Compiler Code
	m_Name.string::string();
}
```
**结论**：
* 如果类未**显示地**定义默认构造函数，编译器将生成一个合成默认构造函数
* 合成默认构造函数会初始化带有默认构造函数的成员变量，不会初始化内置类型

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
```
VS2015: Person::m_Shy::m_Degree = 1, Person::m_Stupid::m_Degree = 2, Person::m_Nervous::m_Degree = 3, Person::m_Age = 2
```

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
虽然显示定义了默认构造函数，但编译器依然会**根据成员变量声明顺序**附加额外的代码，以保证所有成员变量都被初始化。

### 结论
1. 如果未显示定义默认构造函数，编译器将提供一个合成构造函数
2. 编写默认构造函数时，最好显示初始化所有成员变量，特别是内置类型
3. 编译器会自动在默认构造函数中附加未显示定义的成员变量初始化代码，以保证所有成员变量都得到正确的初始化

## 2. 初始化列表（Initializatoin List）
* 普通初始化成员变量方法：
```C++
// C++：
class CWord
{
public:
	CWord()
	{
		m_Word  = "";
		m_Count = 0;
	}

private:
	std::string  m_Word;
	unsigned int m_Count;
};

// 编译器扩张代码：
CWord::Cword()
{
	// m_Word不会被直接初始化，而是被扩张成4个步骤：
	m_Word.string::string();		// 1. 调用m_Word的默认构造函数初始化m_Word
	std::string Temp = std::string("");	// 2. 创建零时对象
	m_Word.string::operator=(Temp);		// 3. 调用string的赋值构造函数赋值m_Word
	Temp.string::~string();			// 4. 销毁零时对象
	
	m_Count = 0；
}
```
* 初始化列表初始化成员变量方法：
```C++
// C++:
class CWord
{
public:
	CWord() : m_Word()
	{
		m_Count = 0;
	}

private:
	std::string  m_Word;
	unsigned int m_Count;
};

// 编译器内部的结果：
CWord::Cword()
{
	// 直接表用string的默认构造函数
	m_Word.string::string();
	m_Count = 0；
}
```
### 结论
1. 使用初始化列表是**直接初始化**成员变量，不会创建中间零时变量
2. 所有的成员变量都应该使用初始化列表初始化（即使是内置类型变量），初始化列表按照成员变量的声明顺序初始化（m_Word先于m_Count，不管m_Word在初始化链表中是否处于m_Count前面，详细参考书P77）
