# 桶排序
**桶排序**是最快最简单的排序，典型的以**空间换时间**的算法。
> 输入一个**非负**数列**范围**在\[0, 1000)，对该数列排序
```C++
template <typename T>
void bucketSort(T* voArray, size_t vArraySize, size_t vMaxNum)
{
	_ASSERT(voArray);
  
	// 申请一个能保存范围在[0, vMaxNum)数列的数组，用空间换时间
	int* Array = new int[vMaxNum]();

	for (size_t i = 0; i < vArraySize; ++i)
	{
		_ASSERT(voArray[i] < vMaxNum);
		// 桶排序的精髓：数组Array的下标对应voArray中的某个数
		++Array[voArray[i]];
	}

	for (size_t i = 0, Index = 0; i < vMaxNum; ++i)
	{
		for (size_t k = 0; k < Array[i]; ++k)
		{
			voArray[Index++] = i;
		}
	}

	delete[] Array;
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
