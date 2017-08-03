# 组合模式
> 将对象组合成**树形结构**以表示“部分-整体”的层级结构，使得用户对**单个对象**和**组合对象**的**使用**具有**一致性**。

**组合模式**通常应用在具有**树形结构**的场景，如树形菜单、文件和文件夹管理等。**组合结构**添加和管理节点及其容易，符合开闭原则。但**组合模式**在使用时，需要将对象强制转换为子类，违背了**依赖倒置原则**，限制了了接口的使用范围。

## 组合模式通用实现
通用基类，函数`commonFun`是内部和叶节点公共逻辑代码：
```C++
class Component
{
public:
	// 内部和叶节点公共逻辑代码
	virtual void commonFun() = 0;
};
```
内部节点，管理内部或叶节点，形成通用树状结构：
```C++
class Composite : public Component
{
public:
	// 添加内部或叶节点
	void add(Component* vComponent)
	{
		m_Components.push_back(vComponent);
	}
	// 删除内部或叶节点
	void remove(Component* vComponent)
	{
		
	}
	// 获取当前分支下的所有子节点
	std::vector<Component*> getChilds()
	{
		return m_Components;
	}

	virtual void commonFun()
	{
		std::cout << "Composite" << std::endl;
	}

private:
	std::vector<Component*> m_Components;
};
```
叶节点，只需单纯实现业务代码，无需管理子节点：
```C++
class Leaf : public Component
{
public:
	virtual void commonFun() override
	{
		std::cout << "leaf" << std::endl;
	}
};
```
客户端，在使用时，需要强制将节点通过**RTTI**转化成对应的类型（内部或叶节点）：
```C++
int main()
{
	Composite* Root = new Composite();
	Composite* InnerNode = new Composite();
	Component* LeafNode1 = new Leaf();
	Component* LeafNode2 = new Leaf();
	Component* LeafNode3 = new Leaf();

	Root->add(InnerNode);
	Root->add(LeafNode1);

	InnerNode->add(LeafNode2);
	InnerNode->add(LeafNode3);

	std::queue<Component*> Queue;
	Queue.push(Root);

	while (!Queue.empty())
	{
		auto pNode = Queue.front();
		Queue.pop();

		pNode->commonFun();

		auto pBranch = dynamic_cast<Composite*>(pNode);
		if (pBranch)
		{
			for (auto pChild : pBranch->getChilds())
			{
				Queue.push(pChild);
			}
		}
	}

	return 0;
}
```
> VS2015： Composite Composite leaf leaf leaf
