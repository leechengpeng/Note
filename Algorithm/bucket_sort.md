# 桶排序
**桶排序**是最快最简单的排序，典型的以**空间换时间**的算法。

**例子**：输入一个**非负**数列**范围**在\[0, 1000)，对该数列排序
```C++
template <typename T>
void bucketSort(T* voArray, size_t vArraySize, size_t vMaxNum)
{
	_ASSERT(voArray);
  
  // Step1：申请一个能保存范围在[0, vMaxNum)数列的数组X，用空间换时间
	int* Array = new int[vMaxNum]();

	for (size_t i = 0; i < vArraySize; ++i)
	{
		_ASSERT(voArray[i] < vMaxNum);
    // 数组Array的xiao'bi
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
