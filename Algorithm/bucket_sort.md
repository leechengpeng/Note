# 桶排序
**桶排序**是最快最简单的排序，典型的以**空间换时间**的算法。算法流程如下述代码：
> 输入一个**非负**数列**范围**在\[0, 1000)，对该数列排序
```C++
template <typename T>
void bucketSort(T* voArray, size_t vArraySize, size_t vMaxNum)
{
	_ASSERT(voArray);
	// 申请一个能容下数列（voArray）中任意数字的桶。
	// 其中，容下的意思是桶的大小至少大于数列中最大的数
	int* Bucket = new int[vMaxNum]();

	for (size_t i = 0; i < vArraySize; ++i)
	{
		_ASSERT(voArray[i] < vMaxNum);
		++Bucket[voArray[i]];
	}

	for (size_t i = 0, Index = 0; i < vMaxNum; ++i)
	{
		for (size_t k = 0; k < Bucket[i]; ++k)
		{
			voArray[Index++] = i;
		}
	}

	delete[] Bucket;
}
```
```C++
int main()
{
	const int ArraySize = 10;
	const int MaxNum    = 100;

	int Array[ArraySize] = {99, 5, 75, 1, 55, 66, 65, 88, 14, 66};
	bucketSort(Array, ArraySize, MaxNum);

	for (size_t i = 0; i < ArraySize; ++i)
	{
		std::cout << Array[i] << " ";
	}
	std::cout << std::endl;

	return 0;
}
```
