# 策略设计模式
> 定义一个类将一组算法（策略）封装起来，使之可以互相切换

**策略模式优点**：本身**扩展性良好**，并**避免过多的条件判断**。

**策略模式缺点**：客户端必须**知道所有策略**，并需自行决定选择哪一个策略类（违反迪米特法则，只想使用一个策略，但必须了解这一套策略的优缺点）。同时，策略过多时，导致类数量增多。

## 实例
通常，会根据不同的数据集属性（是否随机分布）选择不同的排序算法。排序算法的算法，大家最容易想到的是如下代码：
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
