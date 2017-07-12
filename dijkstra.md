# 迪杰斯特拉（Dijkstra）单源最短路径算法
### 算法流程
1. 创建源顶点到距离集合**DistanceSet**并初始化：起始点为0，其他点为无限远
2. 选择**DistanceSet**中未标记的**最短距离顶点S**，然后将其标记
3. 更新**DistanceSet**中**点S**所有邻接顶点得距离值：如果**点S**到其邻接点小于该邻接点之前的距离则更新
4. 循环流程2和流程3直至取不出最短距离点：**所有点都被标记**或者**当前最短距离为无限远**



> 参考：[Dijkstra 单源最短路径算法](http://www.cnblogs.com/gaochundong/p/dijkstra_algorithm.html)
