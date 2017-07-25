[<< 返回设计模式](design_patterns.md)

# 责任链模式
> 有多个对象都有可能处理某个请求，为避免对象和请求对象的耦合，将这个对象**连成一条链**，并沿着这条链传递该请求直至有对象处理它为止。

## 实例
在实际项目开发过程中，经常会根据某个状态值，写出很多`if else`逻辑，并根据不同逻辑调用不用处理方法。比如**公司请假流程**：申请1-2天可由项目组长批准，申请3-5天须通过项目经理批准，申请6-7天必须通过部门经理批准。
```C++
void askForLeave(unsigned vDays)
{
	if (vDays < 3)
	{
		std::cout << "项目组长批准请假..." << std::endl;
	}
	else if (vDays <= 5)
	{
		std::cout << "项目经理批准请假..." << std::endl;
	}
	else if (vDays <= 7)
	{
		std::cout << "部门经理批准请假..." << std::endl;
	}
	else
	{
		std::cout << "公司不允许请假" << vDays << "天" << std::endl;
	}
}
```
