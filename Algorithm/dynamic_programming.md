# 动态规划（Dynamic Programming，DP）
**动态规划算法**常用于**求解具有某种最优解的问题**，具有如下性质：
* **最优子结构**：**当问题的最优解包含了其子问题的最优解时**，称该问题具有最优子结构。
* **重叠的子问题**：当**问题**由**相同的更小子问题构成**，称之为重叠的子问题。
* **空间换时间算法**：重叠子问题之间一般有某种**递推关系**，通常将较小问题的最优解结果**记录在表中**来替换**交叠子问题的重复求解**。

## 实例
### 1. 最大子数组
> Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

**Example**：given the array `[-2,1,-3,4,-1,2,1,-5,4]`, and the contiguous subarray `[4,-1,2,1]` has the largest sum = 6. 

**Link**：[https://leetcode.com/problems/maximum-subarray/description/](https://leetcode.com/problems/maximum-subarray/description/)



#### 参考：
* [国王金矿问题](http://www.cnblogs.com/SDJL/archive/2008/08/22/1274312.html)
