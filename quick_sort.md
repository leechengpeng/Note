## 快速排序
快速排序使用**分治策略**将一个数列分成两个子数列并通过递归来处理，基本流程如下：
1. 从数列中挑出一个**基准值/主元（Pivot）**
2. 将元素值比基准值小的放在基准值前面，比基准值大的放到基准值后面
3. 递归地处理基准值左右两个子序列

#### 示例
1. 整体划分

| 基准值 | 5 | 1 | 12 | -5 | 16 | 6 | -15 | 11 | 12 | 33 | 描述 |
| :---: | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 5 | ***-15*** | 1 | 12 | -5 | 16 | 6 | ***-15*** | 11 | 12 | 33 | 从右往左检索第一个比Pivot小的数 |
| 5 | -15 | 1 | ***12*** | -5 | 16 | 6 | ***12*** | 11 | 12 | 33 | 从左往右检索第一个比Pivot大的数 |
| 5 | -15 | 1 | ***-5*** | ***-5*** | 16 | 6 | 12 | 11 | 12 | 33 | 从右往左检索第一个比Pivot小的数 |
| 5 | -15 | 1 | -5 | ***5*** | 16 | 6 | 12 | 11 | 12 | 33 | 检索完毕，将Pivot置于中间 |

2. 步骤1的左侧子序列

| 基准值 | -15 | 1 | -5 | 描述 |
| :---: | --- | --- | --- | --- |
| -15 | -15 | 1 | -5 | 从右往左检索第一个比Pivot小的数，未检索到停止算法 |

3. 步骤2的右侧子序列(步骤2左侧子序列以只有元素-15不需要继续)

| 基准值 | 1 | -5 | 描述 |
| :---: | --- | --- | --- |
| 1 | **-5** | **-5** | 从右往左检索第一个比Pivot小的数 |
| 1 | -5 | **1** | 检索完毕，将Pivot置于中间 |

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
