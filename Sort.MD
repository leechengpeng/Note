通用代码：
```
// ***********************************************
// FUNCTION
template <typename T, size_t LEN>
std::ostream& operator << (std::ostream& vOut, const std::array<T, LEN>& vArray)
{
	for (auto Element : vArray) { vOut << Element << " "; }

	return vOut;
}

// ***********************************************
// FUNCTION
template <typename T>
inline void swap(T& voX, T& voY)
{
	T Temp = voX;
	voX = voY;
	voY = Temp;
}
```

# 1. 冒泡排序
* 比较相邻元素，如果前者比后者大，则交换两元素
* 重复上诉步骤，直至未排序序列末尾

```
template <typename T, size_t LEN>
void BubbleSort(std::array<T, LEN>& voArray)
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
