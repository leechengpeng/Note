# 桥梁模式
> 将抽象与实现解耦（抽象和实现分离），使得两者可以独立地变化

### 桥梁模式优点
* **抽象和实现分离**：这是桥梁模式的主要特点，它完全是为了**解决继承的缺点**而提出的设计模式。继承具有侵入性，子类必须实现所有基类的方法，而通过**桥梁模式**，可选择使用想要使用的功能。
* **良好的扩展性**：某些类可能有多个变化维度（因素），每个变化因素都可以通过“桥梁”把变化因素（实现）和类连接（抽象），扩展的时候只需要继承实现变化因素的功能即可。

大概实现流程如下，具体细节参考注释：
```C++
// 第一维变化因素
class ChangeFactor1
{
public:
    virtual void doSomething() = 0;
};

// 第一维具体变化因素
class ConcreteChangeFactor1A
{
public:
    virtual void doSomething() override;
};
class ConcreteChangeFactor1B
{
public:
    virtual void doSomething() override;
};

// 第二维变化因素
class ChangeFactor2
{
public:
    virtual void doSomething() = 0;
};

// 第二维具体变化因素
class ConcreteChangeFactor2A
{
public:
    virtual void doSomething() override;
};
class ConcreteChangeFactor2B
{
public:
    virtual void doSomething() override;
};

class ConcreteClass
{
public:
    // 通过构造函数，可以提醒调用在明确（必须）将抽象（成员变量）与细节（具体变化因素）建立“桥梁”连接
    ConcreteClass(ChangeFactor1* vChangeFactor1, ChangeFactor2* vChangeFactor2) : m_ChangeFactor1(vChangeFactor1), m_ChangeFactor2(vChangeFactor2)
    {
    
    }
    
    void doSomething()
    {
        // 具体业务
        m_ChangeFactor1->doSomething();
        m_ChangeFactor2->doSomething();
    }
    
private:
    // 桥梁
    ChangeFactor1* m_ChangeFactor1;
    ChangeFactor2* m_ChangeFactor2;
};

```
