[设计模式](design_patterns.md)

## 单例模式
单例模式定义：**确保类只有一个实例，而且自行实例化并向整个系统提供这个实例**，能有效的减少内存和性能开销。
### 饿汉式
主动创建实例，线程安全，不需要加锁。
```C++
class Singleton
{
public:
	static Singleton* getInstance() { return m_Instance; }
	
protected:
	Singleton() {}
	
private:

	static Singleton* m_Instance;
};

Singleton* Singleton::m_Instance = new Singleton();
```
### 懒汉式
被动创建，线程不安全，需要加锁（双重判空保证唯一性）。
```C++
class Singleton
{
public:
	static Singleton* getInstance() 
	{ 
		if (m_Instance == NULL) { m_Instance = new Singleton(); }
		return m_Instance; 
	}
	
protected:
	Singleton() {}
	
private:
	static Singleton* m_Instance;
};

Singleton* Singleton::m_Instance = NULL;
```
### 通用单例模式：模板 + 继承
```C++
template <typename T>
class Singleton
{
public:
	static T* getInstance() { return m_Instance; }

protected:
	Singleton() {}

private:
	static T* m_Instance;
};

template <typename T>
T* Singleton<T>::m_Instance = new T();
```
```C++
class Emperor : public Singleton<Emperor>
{
public:
	void say() { std::cout << "balabala..." << std::endl; }

protected:
	Emperor() {}

	friend class Singleton<Emperor>;
};

int main()
{
	Emperor::getInstance()->say();

	return 0;
}
```
> VS2015: balabala...

注意**子类**需要将**基类**申明成**友元类**以调用子类的构造函数。
