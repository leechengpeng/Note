## 对象模型（Object Model）
C++中有以下几种成员（Member）类型：
> * 成员变量类型：静态成员变量（Static）和非静态成员变量（Nonstatic）
> * 成员函数类型：静态成员函数（Static）、非静态成员函数（Nonstatic）和虚函数（Virtual）

考虑如下一维点的类型设计：
```C++
class CPoint
{
public:
	CPoint(int vX = 0);
	virtual ~CPoint();  		// 虚函数

	float x() const;  		// 成员函数（非静态成员函数）
	static unsigned getNumsPoint(); // 静态成员函数
	
private:
	float m_X;  			// 成员变量（非静态成员变量）
	static unsigned m_NumsPoint;  	// 静态成员变量
};
```
类`CPoint`中拥有了所有C++类可能的成员类型，如何设计其对象模型才能降低类带来的管理成本（相对于C）？
### 简单对象模型
