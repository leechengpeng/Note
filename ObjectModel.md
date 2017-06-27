## 对象模型（Object Model）
C++中有以下几种成员（Member）类型：
> * 成员变量类型：静态成员变量（Static）和非静态成员变量（Nonstatic）
> * 成员函数类型：静态成员函数（Static）、非静态成员函数（Nonstatic）和虚函数（Virtual）

考虑如下一维点类型的设计：
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
