# 观察者模式
**观察者模式**也叫**发布订阅模式**，它是项目中经常使用的模式，其定义如下：
> 定义对象间一种**一对多**的依赖关系，使得每当一个对象（被观察者）状态改变，则所有依赖于它的对象（观察者）都会得到通知并被自动更新。

### 应用场景
* **文件系统**：在一个目录下添加了一个文件，这个操作会通知目录管理器目录发生了变化
* **ATM取钱**：多次在ATM机上密码输入错误，此时，ATM机通知摄像头进行拍摄，并通知监控系统发生吞卡操作。

### 实例：订阅报纸
```C++
// 观察者/订阅
class Subscriber
{
public:
	Subscriber(const std::string& vName) : m_Name(vName) {}
	void hasNewsPaper() const
	{
		std::cout << m_Name << "，有新的China Daily，请注意查收！" << std::endl;
	}

	bool operator==(const Subscriber& vSubscriber)
	{
		return m_Name == vSubscriber.m_Name;
	}

private:
	std::string m_Name;
};
```

```C++
// 被观察者/发布者
class NewsPaper
{
public:
	virtual void addSubscriber(Subscriber& vSubscriber) = 0;
	virtual void removeSubscriber(Subscriber& vSubscriber) = 0;
	virtual void sendPaper() = 0;
};

class ChinaDaily : public NewsPaper
{
public:
	virtual void addSubscriber(Subscriber& vSubscriber) override
	{
		m_SubscriberSet.push_back(vSubscriber);
	}

	virtual void removeSubscriber(Subscriber& vSubscriber) override
	{
		m_SubscriberSet.remove(vSubscriber);
	}

	virtual void sendPaper() override
	{
		for (auto& Subscriber : m_SubscriberSet)
		{
			Subscriber.hasNewsPaper();
		}
	}

private:
	std::list<Subscriber> m_SubscriberSet;
};
```
```C++
// 测试
int main()
{
	Subscriber Tom("Tom");
	Subscriber Lucy("Lucy");
	Subscriber Jack("Jack");

	NewsPaper* pNewsPaper = new ChinaDaily();
	// 添加订阅者
	pNewsPaper->addSubscriber(Tom);
	pNewsPaper->addSubscriber(Lucy);
	pNewsPaper->addSubscriber(Jack);

	std::cout << "2017.4.30发放订阅" << std::endl;
	// 发放报纸给订阅者
	pNewsPaper->sendPaper();
	std::cout << "报纸发放完毕" << std::endl;

	std::cout << "2017.5.1发放订阅" << std::endl;
	// Tom没有续订，将其移除
	pNewsPaper->removeSubscriber(Tom);
	// 发放报纸给订阅者
	pNewsPaper->sendPaper();
	std::cout << "报纸发放完毕" << std::endl;

	return 0;
}
```
