# 动态规划（Dynamic Programming，DP）
**动态规划算法**常用于**求解具有某种最优解的问题**，具有如下性质：
* **最优子结构**：**当问题的最优解包含了其子问题的最优解时**，称该问题具有最优子结构。
* **重叠的子问题**：当**问题**由**相同的更小子问题构成**，称之为重叠的子问题。
* **空间换时间算法**：重叠子问题之间一般有某种**递推关系**，通常将较小问题的最优解结果**记录在表中**来替换**交叠子问题的重复求解**。

关于动态规划的详细概念引导，推荐阅读**刘永辉**的[《通过金矿模型介绍动态规划》](http://www.cnblogs.com/SDJL/archive/2008/08/22/1274312.html)。

## 实例
### 1. Maximum Subarray
> Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

**Example**：given the array `[-2,1,-3,4,-1,2,1,-5,4]`, and the contiguous subarray `[4,-1,2,1]` has the largest sum = 6. 

**Link**：[https://leetcode.com/problems/maximum-subarray/description/](https://leetcode.com/problems/maximum-subarray/description/)

### 解法：
这道题求解最大子数组和，典型的求解最优解的问题，可以通过**动态规划思想**分解该问题：
```C++
#include <vector>
#include <algorithm>

int maxSubArray(const std::vector<int>& vNums) {

	int Max  = vNums[0];
	int Temp = vNums[0];
	
	for (size_t i = 1; i < vNums.size(); i++)
	{
		Temp = std::max(vNums[i], vNums[i] + Temp);
		Max = std::max(Temp, Max);
	}

	return Max;
}
```
// 将一个大问题（N个元素的数组Array）转化为较小的问题（N-1个元素的数组）
