[<< 返回设计模式](design_patterns.md)

# 责任链模式
> 有多个对象都有可能处理某个请求，为避免对象和请求对象的耦合，将这个对象**连成一条链**，并沿着这条链传递该请求直至有对象处理它为止。

## 实例
**某公司批假权限如下**：

| 职位 | 可批假天数范围 |
| --- | :---: |
| 项目组长 | 1-2天 |
| 项目经理 | 3-4天 |
| 部门经理 | 5-6天 |

请假1-2天可直接由项目组长批准，但如果请假3-4天，就必须请求项目经理批准。当然不可能越级批准，批小假就不必劳烦领导对吧？

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
该逻辑最大的问题不是太多的`if else`判断，而是违背**开闭原则**。如果公司添加一项新的福利，只要员工的年假时间允许，总经理可以批准\[7, 9)天的假，这个时候就不得不原有逻辑上添加新的判断，导致原有代码被修改。

### 责任链
首先设计员工的基类：
```C++
class Employee
{
public:
	Employee(const std::string& vEmployeeType) : m_EmployeeType(vEmployeeType), m_NextSupervisor(nullptr) {}

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
基类中的**m_NextSupervisor**实际上就是责任链de
