## 1. 交换排序
### 1.1. 冒泡排序
#### 算法描述
* 比较相邻元素，如果前者比后者大，则交换两元素
* 重复上诉步骤，直至未排序序列末尾
#### 流程
**第一次迭代**
1. ***5 1*** 12 -5 16
2. 1 ***5 12*** -5 16
3. 1 5 ***12 -5*** 16
4. 1 5 ***-5 12*** 16
5. 1 5 -5 ***12 16***

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
### 插入排序
