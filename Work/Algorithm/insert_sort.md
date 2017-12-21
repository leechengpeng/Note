# 插入排序

## 直接插入排序（稳定排序，O(n2)） *
### 算法描述
对于未排序数据，在已排序序列中**从后向前扫描**，找到相应位置，将位置后的已排序数据逐步向后挪位，将新元素插入到该位置。
### 算法流程
> * `5` | ***1*** 12 -5 16
> * `1 5` | ***12*** -5 16
> * `1 5 12` | ***-5*** 16
> * `-5 1 5 12` | ***16*** 
> * `-5 1 5 12 16` |

```C++
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

## 希尔排序（不稳定排序，O(nlogn2)）
希尔排序是插入排序的一种更高效的改进版，基于插入排序的特点提出改进方法的：
* 插入排序对已近乎排好序的数据进行排序时，可以达到线性时间
* 插入排序一般来说比较低效，每次只能将数据移动一位
#### 算法描述
希尔排序通过将比较的全部元素分为**几个区域**来提升插入排序的性能。这样可以让一个元素一次性地朝最终位置前进一大步。然后算法再取越来越小的步长进行排序，算法的最后一步就是普通的插入排序，但是到了这步，需排序的数据几乎是已排好的了，此时插入排序较快。
#### 步长
步长的选择是希尔排序的重要部分，算法开始需要以一定步长大小进行排序，然后缩短步长，最终算法以步长为1进行排序（步长为1时，算法退化为插入排序）。当前已知比较好的步长串是由Sedgewick 提出的 (1, 5, 19, 41, 109,...)步长序列，这样的步长在小数据量序列中排序速度甚至超过快速排序。
```C++
template <typename T, size_t LEN>
void shell_sort(std::array<T, LEN>& voArray)
{
	size_t Gap = static_cast<size_t>(ceil(voArray.size() / 2.));

	while (Gap > 0)
	{
		for (size_t i = 0; i < voArray.size(); ++i)
		{
			T Temp = voArray[i];

			size_t k = i;
			while (k >= Gap && voArray[k - Gap] > Temp)
			{
				voArray[k] = voArray[k - Gap];
				k -= Gap;
			}

			voArray[k] = Temp;
		}

		Gap = static_cast<size_t>(floor(0.5 + Gap / 2.2));
	}
}
```

参考文献：
* [比较排序算法 —— by 匠心十年](http://www.cnblogs.com/gaochundong/p/comparison_sorting_algorithms.html)
