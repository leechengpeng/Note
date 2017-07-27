# 策略模式
> 定义一个类将一组算法（策略）封装起来，使之可以互相切换

**策略模式优点**：本身**扩展性良好**，并**避免过多的条件判断**。

**策略模式缺点**：客户端必须**知道所有策略**，并需自行决定选择哪一个策略类（违反迪米特法则，只想使用一个策略，但必须了解这一套策略的优缺点）。同时，策略过多时，导致类数量增多。

## 实例
通常，会根据数据集的属性（随机分布）选择不同的排序算法。排序算法的算法，大家最容易想到的是如下代码：
```C++
template <typename T>
void bubbleSort(T* voArray, unsigned vArraySize)
{

}

template <typename T>
void insertionSort(T* voArray, unsigned vArraySize)
{

}


template <typename T>
void selectionSort(T* voArray, unsigned vArraySize)
{

}

template <typename T>
void sort(T* voArray, unsigned vArraySize, const std::string& vSortType)
{
	if (vSortType == "Bubble")
	{
		bubbleSort(voArray, vArraySize);
	}
	else if (vSortType == "Insertion")
	{
		insertionSort(voArray, vArraySize);
	}
	else if (vSortType == "Selection")
	{
		selectionSort(voArray, vArraySize);
	}
	else
	{
		std::cerr << "Sort type error: " << vSortType << std::endl;
	}
}
```
这段代码不符合**开闭原则**，在添加排序算法的同时，必须不断的在原有代码添加`if else`。

### 策略模式
不同的排序算法/策略设计：
```C++
template <typename T>
class SortAlgorithm
{
public:
	virtual void sort(T* voArray, unsigned vArraySize) const = 0;
};

template <typename T>
class BubbleSort : public SortAlgorithm<T>
{
public:
	virtual void sort(T* voArray, unsigned vArraySize) const override
	{
		std::cout << "调用冒泡排序..." << std::endl;
	}
};

template <typename T>
class InsertionSort : public SortAlgorithm<T>
{
public:
	virtual void sort(T* voArray, unsigned vArraySize) const override
	{
		std::cout << "调用插入排序..." << std::endl;
	}
};

template <typename T>
class SelectionSort : public SortAlgorithm<T>
{
public:
	virtual void sort(T* voArray, unsigned vArraySize) const override
	{
		std::cout << "调用选择排序..." << std::endl;
	}
};
```
设计类`SortAlgorithmHandler`选择不同的排序算法/策略：
```C++
template <typename T>
class SortAlgorithmHandler
{
public:
	SortAlgorithmHandler() : m_SortAlgorithm(nullptr) {}

	void sort(T* voArray, unsigned vArraySize)
	{
		_ASSERT(voArray && m_SortAlgorithm);
		m_SortAlgorithm->sort(voArray, vArraySize);
	}

	void setSortAlgorithm(SortAlgorithm<T>* vSA)
	{
		_ASSERT(vSA);
		m_SortAlgorithm = vSA;
	}

private:
	SortAlgorithm<T>* m_SortAlgorithm;
};
```
客户端：
```C++
template <typename T>
void sort(T* voArray, unsigned vArraySize, const std::string& vSortType)
{
	// 此处可使用工厂模式，根据不同的vSortType创建出不同的排序算法类
	// 类似于'SortAlgorithm<T>* pSA = Factory.create(vSortType);'
	SortAlgorithm<T>* pSA = new BubbleSort<T>();

	SortAlgorithmHandler<T> pSAH;
	pSAH.setSortAlgorithm(pSA);
	pSAH.sort(voArray, vArraySize);
}
```
注意客户端还是需要通过不同的**vSortType**创建不同排序算法/策略实例，这个时候[工厂模式](factory.md)就发挥其作用。当添加新的排序算法时，不需要改变**sort**函数的任何代码即可完成扩展。
