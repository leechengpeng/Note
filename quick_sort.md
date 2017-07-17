## 快速排序
快速排序使用**分治策略**将一个数列分成两个子数列并通过递归来处理，基本流程如下：
1. 从数列中挑出一个**基准值/主元**（随机/中分等）
2. 将元素值比基准值小的放在基准值前面，比基准值大的放到基准值后面
3. 递归地处理基准值左右两个子序列

#### 示例


#### 代码实现
```C++
template <typename T, size_t LEN>
inline void quick_sort(std::array<T, LEN>& voArray)
{
	_quick_sort(voArray, 0, voArray.size() - 1);
}

template <typename T, size_t LEN>
void _quick_sort(std::array<T, LEN>& voArray, size_t vStart, size_t vEnd)
{
	T Pivot = voArray[vStart];
	size_t i = vStart, k = vEnd;

	while (i < k)
	{
		// 从右向左检索第一个比Pivot小的数
		while (i < k && voArray[k] > Pivot) { --k; }
		if (i < k) { voArray[i++] = voArray[k]; }

		// 从左往右检索第一个比Pivot大的数
		while (i < k && voArray[i] < Pivot) { ++i; }
		if (i < k) { voArray[k--] = voArray[i]; }
	}

	voArray[i] = Pivot;

	if (i > 0 && vStart < i - 1) { _quick_sort(voArray, vStart, i - 1); }
	if (i + 1 < vEnd) { _quick_sort(voArray, i + 1, vEnd); }
}
```
