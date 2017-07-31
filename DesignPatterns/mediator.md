# 中介者模式
> 使用**中介对象**（中介者）来封装**一系列对象**的**交互**。中介者使各对象不需要显示的相互作用，从而使其耦合松散，而且可以通过中介者独立地改变它们之间的交互。

### 实例
房屋中介就是最典型的中介者模式。租客向房屋中介咨询房屋信息，中介根据租客的情况选择合适的房屋推荐给租客。当有租客想租某一件房屋时，这个时候中介再联系房东商量租房事宜。租客和房东的沟通基本都是通过房屋中介进行：
```C++
// 房屋中介：建立起租客和房东的联系
class HouseMediator
{
public:
	virtual void contact(const std::string& vMessage, const std::string& vRole) const = 0;

	void setLandlord(Landlord* vLandlord)
	{
		m_Landlord = vLandlord;
	}

	void setTenant(Tenant* vTenant)
	{
		m_Tenant = vTenant;
	}

	const Landlord* getLandlord() const { return m_Landlord; }
	const Tenant*   getTenant() const { return m_Tenant; }

private:
	Tenant*   m_Tenant;
	Landlord* m_Landlord;
};

// 具体房屋中介：链家
class LianJia : public HouseMediator
{
public:
	// 中介联系租客和房东
	void contact(const std::string& vMessage, const std::string& vRole) const override
	{
		if (vRole == "Landlord")
		{
			getTenant()->getMessage(vMessage);
		}
		else if (vRole == "Tenant")
		{
			getLandlord()->getMessage(vMessage);
		} 
		else
		{
			std::cout << "Unknown operation..." << std::endl;
		}
	}
};
```
租客和房东通过房屋中介互相联系：
```C++
// 房东
class Landlord
{
public:
	// 房东通过中介与租客联系
	Landlord(const std::string& vName, HouseMediator* vMediator) : m_Mediator(vMediator), m_Name(vName) 
	{
		_ASSERT(m_Mediator);
		m_Mediator->setLandlord(this);
	}

	void contact(const std::string& vMessage) const
	{
		m_Mediator->contact(vMessage, "Landlord");
	}

	void getMessage(const std::string& vMessage) const
	{
		std::cout << m_Name << "（房东）收到租客信息：" << vMessage << std::endl;
	}

private:
	HouseMediator* m_Mediator;
	std::string    m_Name;
};

// 租客
class Tenant
{
public:
	// 租客通过中介与房东联系
	Tenant(const std::string& vName, HouseMediator* vMediator) : m_Mediator(vMediator), m_Name(vName)
	{
		_ASSERT(m_Mediator);
		m_Mediator->setTenant(this);
	}

	void contact(const std::string& vMessage) const
	{
		m_Mediator->contact(vMessage, "Tenant");
	}

	void getMessage(const std::string& vMessage) const
	{
		std::cout << m_Name << "（租客）收到房东信息：" << vMessage << std::endl;
	}

private:
	HouseMediator* m_Mediator;
	std::string    m_Name;
};
```
应用：
```C++
int main()
{
	HouseMediator* pMediator = new LianJia();

	Tenant   Tom("Tom", pMediator);
	Landlord Lucy("Lucy", pMediator);

	Tom.contact("我想租一套房...");
	Lucy.contact("可以，我的房屋面积100平米，一个月4000元...");

	return 0;
}
```
> Lucy（房东）收到租客信息：我想租一套房...

> Tom（租客）收到房东信息：可以，我的房屋面积100平米，一个月4000元...
