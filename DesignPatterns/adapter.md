# 适配器模式
> 将一个类A的接口变成客户端B（适配对象）所期待的另一种接口，从而使原本的接口不匹配而无法在一起工作的两个类能够在一起工作。

适配器模式能够让两个没有太多联系的类工作在一起，提高了类的**复用性**和**灵活性**。适配器的实现模板如下：
```C++
class Target
{
public:
	virtual void request() = 0;
};

class ConcreteTarget : public Target
{
public:
	virtual void request() override { }
};

class Adaptee
{
public:
	void doSomething() { }
};

class Adapter : public Target, public Adaptee
{
public:
	virtual void request() override
	{
		doSomething();
	}
};

int main()
{
	Target* pTarget = new ConcreteTarget();
	pTarget->request();

	return 0;
}
```
如果某一天业务需求变了，需要适配器的逻辑，只需要在客户端修改如下代码即可：
```C++
Target* pTarget = new Adapter();
```

## 实例
**STL**中的**stack**就是一个典型的**适配器（Adapter）**，它以其他容器（vector或list）作为低层实现，将其他类的接口**适配**到自己所需的业务中。下面是一个简单版本的stack实现：
```C++
// 将Adaptee的接口适配到stack上
template <typename T, typename Adaptee = std::vector<T>>
class stack
{
public:
	void push(const T vData)
	{
		m_Stack.push_back(vData);
	}

	void pop()
	{
		m_Stack.pop_back();
	}

	T top() const
	{
		return m_Stack.back();
	}

	bool empty() const
	{
		return m_Stack.empty();
	}

private:
	Adaptee m_Stack;
};
```
客户端代码：
```C++
int main()
{
	stack<int> Stack;
	Stack.push(1);
	Stack.push(3);
	Stack.push(5);

	while (!Stack.empty())
	{
		std::cout << Stack.top() << std::endl;
		Stack.pop();
	}

	return 0;
}
```
> VS2015：5 3 1

只要是实现了`back`、`push_back`、`pop_back`和`empty`方法的容器都可以适配到`stack`上，如`vector`、`deque`或`list`。
```
stack<int, std::list<int>>  Stack;
stack<int, std::deque<int>> Stack;
```
