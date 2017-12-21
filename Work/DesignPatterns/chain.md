# 责任链模式
> 有多个对象都有可能处理某个请求，为避免对象和请求对象的耦合，将这个对象**连成一条链**，并沿着这条链传递该请求直至有对象处理它为止。

**责任链模式**将请求和处理解耦，请求者不需要知道谁可以处理其请求，提高了系统的灵活性。缺点是：任何请求都需要从头到尾遍历，性能有一定问题。

## 实例
**某公司批假权限如下**：

| 职位 | 可批假天数范围 |
| --- | :---: |
| 项目组长 | 1-2天 |
| 项目经理 | 3-4天 |
| 部门经理 | 5-6天 |

请假1-2天可直接由项目组长批准，但如果请假3-4天，就必须向项目经理请求批准。当然不可能越级批准，批小假就不必劳烦领导对吧？

### 普通实现
```C++
void askForLeave(unsigned vDays)
{
	if (vDays < 3)
	{
		std::cout << "项目组长批准请假..." << std::endl;
	}
	else if (vDays < 5)
	{
		std::cout << "项目经理批准请假..." << std::endl;
	}
	else if (vDays < 7)
	{
		std::cout << "部门经理批准请假..." << std::endl;
	}
	else
	{
		std::cout << "公司不允许请假" << vDays << "天" << std::endl;
	}
}
```
该逻辑最大的问题不是太多的`if else`判断，而是违背**开闭原则**。如果公司添加一项新的福利，只要员工的年假时间允许，总经理可以批准7-8天的假，这个时候就不得不原有逻辑上添加新的判断，导致原有代码被修改。
```C++
void askForLeave(unsigned vDays)
{
	if (vDays < 3)
	{
		std::cout << "项目组长批准请假..." << std::endl;
	}
	else if (vDays < 5)
	{
		std::cout << "项目经理批准请假..." << std::endl;
	}
	else if (vDays < 7)
	{
		std::cout << "部门经理批准请假..." << std::endl;
	}
	// 添加新的代码逻辑
	else if (vDays < 9)
	{
		std::cout << "总经理批准请假..." << std::endl;
	}
	else
	{
		std::cout << "公司不允许请假" << vDays << "天" << std::endl;
	}
}
```

### 责任链
首先设计员工的基类：
```C++
class Employee
{
public:
	Employee(const std::string& vEmployeeType) : m_EmployeeType(vEmployeeType), m_NextSupervisor(nullptr) {}

	// 模板方法模式 & 责任链模式
	void approveLeave(unsigned vDays) const
	{
		if (_approveLeaveV(vDays))
		{
			std::cout << m_EmployeeType << "批准了你" << vDays << "天的请假请求..." << std::endl;
		}
		else
		{
			if (m_NextSupervisor != nullptr)
			{
				m_NextSupervisor->approveLeave(vDays);
			}
			else
			{
				std::cout << "公司不允许请假" << vDays << "天" << std::endl;
			}
		}
	}

	// 里氏替换原则 & 依赖倒置原则
	void setNextSupervisor(Employee* vNextSupervisor)
	{
		m_NextSupervisor = vNextSupervisor;
	}

protected:
	virtual bool _approveLeaveV(unsigned vDays) const = 0;

private:
	std::string m_EmployeeType;
	Employee*   m_NextSupervisor;
};
```
基类中的**m_NextSupervisor**实际上就是责任链，指向下一个可负责的责任人。具体责任人实现如下：
```C++
class TeamLeader : public Employee
{
public:
	TeamLeader() : Employee("项目组长") {}

protected:
	virtual bool _approveLeaveV(unsigned vDays) const override
	{
		return vDays == 1 || vDays == 2;
	}
};

class ProjectManager : public Employee
{
public:
	ProjectManager() : Employee("项目经理") {}

protected:
	virtual bool _approveLeaveV(unsigned vDays) const override
	{
		return vDays == 3 || vDays == 4;
	}
};

class BranchManager : public Employee
{
public:
	BranchManager() : Employee("部门经理") {}

protected:
	virtual bool _approveLeaveV(unsigned vDays) const override
	{
		return vDays == 5 || vDays == 6;
	}
};
```
客户端
```C++
void approveLeaveSystem(unsigned vDays)
{
	if (vDays == 0) return;

	static Employee* pTeamLeader     = new TeamLeader();
	static Employee* pProjectManager = new ProjectManager();
	static Employee* pBranchManager  = new BranchManager();

	pTeamLeader->setNextSupervisor(pProjectManager);
	pProjectManager->setNextSupervisor(pBranchManager);

	pTeamLeader->approveLeave(vDays);
}
```
这个时候如果添加新的责任人，只需要添加一个新的责任链和责任人即可。
