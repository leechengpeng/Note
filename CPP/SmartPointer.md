# 使用对象管理资源（Use objects to manage resources）

通常都会写到如下代码：
```
class CPerson
{

};

void function()
{
	CPerson* pTom = new CPerson();

	// Lots of other codes...

	delete pTom;
}
```
首先，创建了一个指向`CPerson`的指针`pTom`，然后在中间编写了大量代码，最后销毁对象占用的内存。这段代码初看起来可能没什么问题，但可能有很多原因都可以导致资源得不到释放：
* 销毁资源前，被代码开发人员或维护人员无意中调用了return，并未注意到后面的资源释放操作
* 销毁资源前，程序抛出异常，导致资源得不到释放

为了确保资源得到正确的释放，C++中有一个比较好的特性，可以用于资源的管理，那就是类的析构函数机制。标准模板库中，提供了几个基于对象来管理资源的类：auto_ptr、shared_ptr，这些类也被称为智能指针。

使用对象管理资源有两个关键性设计：
* 创建的资源会被放入管理对象进行管理
* 管理对象使用析构函数对资源进行释放
```
class CPerson
{

};

void function()
{
	std::auto_ptr<CPerson> pTom(new CPerson());
}
```
上述代码中，当function函数执行完成后，`pTom`由于离开其作用域，其在堆上分配的资源会被`auto_ptr`的析构函数自动释放掉。

`注：智能指针相关知识，详细请参考boost或STL智能指针的设计`
