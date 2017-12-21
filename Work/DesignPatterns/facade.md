# 门面模式
> 提供一个高层次的统一访问接口

门面模式优点：外界只能通过统一的**接口**（门面）**访问系统**，不许要了解系统内部过多细节，系统内部更换算法也不会影响客户代码的更改，提高了系统的**灵活性和安全性**。

```C++
class Step1
{
public:
    void doStep1();
};

class Step2
{
public:
    void doStep2();
};

class Step3
{
public:
    void doStep3();
};

// 门面对象
public Facade
{
public:
    // 门面1
    static void Method1()
    {
        // 提供统一接口Method1，内部细节被掩盖，提高设计的灵活性（动态更换实现细节）
        m_Step1.doStep1();
        m_Step2.doStep2();
        m_Step3.doStep3();
    }
    
    // 门面2
    static void Method2()
    {
        m_Step3.doStep3();
        m_Step1.doStep1();
    }
    
private:
    Step1 m_Step1;
    Step2 m_Step2;
    Step3 m_Step3;
};
```
