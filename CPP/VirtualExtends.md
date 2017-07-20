## 虚拟继承
**虚拟继承**是**多重继承**中特有的概念，为了解决砖石/菱形继承而出现。
``` C++
class Animal
{
public:
	
	int getWeight() const { return 0; }
};

class Tiger : public Animal {};
class Lion : public Animal {};
class Liger : public Tiger, public Lion {};

int main()
{
	Liger liger;
	liger.getWeight();  // VS2015：错误：Liger::getWeight不明确

	return 0;
}
```
上述代码在任何编译器中都无法编译通过。因为该继承体系中，**Liger**多重继承了**Tiger**和**Lion**，**Liger**类会有两份**Animal**类的成员（数据和方法），从而导致编译器并不知道是调用Tiger类的getWeight()还是调用Lion类的getWeight()。

此时采用**虚拟继承**的方式来保证只有一个`Animal`的对象会被创建：
``` C++
class Animal
{
public:
	Animal() { std::cout << "Construct Animal..." << std::endl; }
};

class Tiger : public virtual Animal {};
class Lion : public virtual Animal {};
class Liger : public Tiger, public Lion {};

int main()
{
	Liger liger;	// 输出一次"Construct Animal..."

	return 0;
}
```
如果不使用虚拟继承：
``` C++
class Animal
{
public:
	Animal() { std::cout << "Construct Animal..." << std::endl; }
};

class Tiger : public Animal {};
class Lion : public Animal {};
class Liger : public Tiger, public Lion {};

int main()
{
	Liger liger;	// 输出两次"Construct Animal..."

	return 0;
}
```


> 注：**虚拟继承**在一般的应用中很少用到，所以也往往被忽视，这也主要是因为在C++中，**多重继承是不推荐的**，也并不常用，而一旦离开了多重继承，虚拟继承就完全失去了存在的必要因为这样只会降低效率和占用更多的空间。
