## 1. 交换排序
### 1.1. 冒泡排序
#### 算法流程
**第一次迭代**
* ***5 1*** 12 -5 16
* 1 ***5 12*** -5 16
* 1 5 ***12 -5*** 16
* 1 5 ***-5 12*** 16
* 1 5 -5 ***12 16***

```
template <typename T, size_t LEN>
void bubble_sort(std::array<T, LEN>& voArray)
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


## 2. 插入排序
### 2.1. 插入排序*
#### 算法描述
对于未排序数据，在已排序序列中**从后向前扫描**，找到相应位置，将位置后的已排序数据逐步向后挪位，将新元素插入到该位置。
#### 算法流程
* `5` | ***1*** 12 -5 16
* `1 5` | ***12*** -5 16
* `1 5 12` | ***-5*** 16
* `-5 1 5 12` | ***16*** 
* `-5 1 5 12 16` |

```
template <typename T, size_t LEN>
void insert_sort(std::array<T, LEN>& voArray)
{
	for (size_t i = 1; i < voArray.size(); ++i)
	{
		if (voArray[i] < voArray[i - 1])
		{
			T Key = voArray[i];

			size_t k = i;
			for (; k > 0 && Key < voArray[k - 1]; --k)
			{
				voArray[k] = voArray[k - 1];
			}

			voArray[k] = Key;
		}
	}
}
```
