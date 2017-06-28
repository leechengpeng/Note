# 构造函数语义 （The Semantics of Constructors）

## 1. 默认构造函数（Default Constructor）的构造操作
首先，先观察如下代码：
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
> VS2015: Person::m_Age = 3435973836, Person::Shy::m_Degree = 1

类**Person**没有提供默认构造函数，在执行`Person Tom;`代码段时，编译器将合成一个默认构造函数，类似于：
```C++
// 编译器合成默认构造函数
inline Person::Person()
{
  // 编译器附加上的Compiler Code
	Shy.Shy::Shy();
}
```
注意编译器合成默认构造函数并没有初始化内置类型**int**的数据，编译器只会
