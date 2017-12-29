# 地形渲染

## 1. 地形表示形式
真实世界的地形数据通常来至摇杆或卫星获取（remotesensing aircraft and satellites），人工地形数据来至美工或则代码直接生成。根据不同的使用场景，地形的表示形式也有所不同：
* **Height Maps**：Height maps是使用最为广泛的地形数据表示形式。一张Height map也被称作高度场（height field），可以使用灰度图表示高度场，图中的每一个像素点都表示一个位置的高度。将高度图放置到一个水平面上，并使用高度图中的像素替换掉其水平面对应位置的高度。这些被替换的的像素称之为posts。Height maps被广泛使用的原因是其表示形式简单，数据来源丰富。
* **Triangulated Irregular Networks(TIN)**：TIN网格由一系列点云构成的没有缝隙的三角形网格。用于构建TINs网格的点云通常由飞行器通过LiDAR（Light detection and ranging）捕获。LiDAR是一种基本的摇杆技术，通过飞行器定时发射激光扫描地形表面捕获地形数据。TINs网格是不规则网格，对于平缓的地形使用较大的三角面片表示，而对于凹凸不平的地形使用较小的三角面片表示以描绘地形的细节。
* **Voxels**
* **Implicit Surfaces**
