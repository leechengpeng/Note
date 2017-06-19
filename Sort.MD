# 1. 冒泡排序
* 比较相邻元素，如果前者比后者大，则交换两元素
* 重复上诉步骤，直至未排序序列末尾
```
template <typename T, size_t LEN>
void bubbleSort(std::array<T, LEN>& voArray)
{
	for (size_t i = LEN - 1; i > 0; --i)
	{
		for (size_t k = 0; k < i; ++k)
		{
			if (voArray[k] > voArray[k + 1])
			{
				swap(voArray[k], voArray[k+1]);
			}
		}
	}
}
```
