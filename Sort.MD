# 1. 冒泡排序
* 比较相邻元素，如果前者比后者大，则交换两元素
* 重复上诉步骤，直至未排序序列末尾

```
flow
st=>start: Start
i=>inputoutput: 输入年份n
cond1=>condition: n能否被4整除？
cond2=>condition: n能否被100整除？
cond3=>condition: n能否被400整除？
o1=>inputoutput: 输出非闰年
o2=>inputoutput: 输出非闰年
o3=>inputoutput: 输出闰年
o4=>inputoutput: 输出闰年
e=>end
st->i->cond1
cond1(no)->o1->e
cond1(yes)->cond2
cond2(no)->o3->e
cond2(yes)->cond3
cond3(yes)->o2->e
cond3(no)->o4->e
```

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
