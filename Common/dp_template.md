# 模板方法模式（Template Method Pattern）
**模板方法模式**是基于代码复用的基本技术，其定义如下：
> 基类中定义一个**模板方法**，**模板方法**调用了一系列**基本方法**（子类负责实现），完成固定逻辑
```C++
class AbstractClass
{
public:
    void templateMethod() const
    {
        method1();
        method2();
    }
    
protected:
    virtual void method1();
    virtual void method2();
};
```
