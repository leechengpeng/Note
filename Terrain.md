# 地形渲染

## 1. 地形表示形式
真实世界的地形数据通常来至摇杆或卫星获取（remotesensing aircraft and satellites），人工地形数据来至美工或则代码直接生成。根据不同的使用场景，地形的表示形式也有所不同：
* **Height Maps**：Height maps是使用最为广泛的地形数据表示形式。一张Height map也被称作高度场（height field），可以使用灰度图表示高度场，图中的每一个像素点都表示一个位置的高度。将高度图放置到一个水平面上，并使用高度图中的像素替换掉其水平面对应位置的高度。这些被替换的的像素称之为posts。Height maps被广泛使用的原因是其表示形式简单，数据来源丰富。
* **Triangulated Irregular Networks(TIN)**：TIN网格由一系列点云构成的没有缝隙的三角形网格。用于构建TINs网格的点云通常由飞行器通过LiDAR（Light detection and ranging）捕获。LiDAR是一种基本的摇杆技术，通过飞行器定时发射激光扫描地形表面捕获地形数据。TINs网格是不规则网格，对于平缓的地形使用较大的三角面片表示，而对于凹凸不平的地形使用较小的三角面片表示以描绘地形的细节。因此，存放一定面积的地形数据相较于Height Maps，TINs使用的内存更少。
* **Voxels**：TODO
* **Implicit Surfaces**：TODO

## 2. 大规模地形渲染
对于大规模地形渲染来说，需要极大的空间存储地形数据。因此，地形及其纹理数据都需要通过特别的技术进行处理。通常解决该问题的方法是在有限的存储空间上进行相关的预处理：
* LOD技术：减少待几何形状的复杂度，或者降低着色的代价。同时，也可以通过裁剪来去除不可见的部分。
* Out-of-Core: 只有部分数据被保存到内存中，其余的数据被存放到硬盘中。根据视点的信息重新视点位置的数据，并将之前的旧的数据移出。

### 2.1 Level of Detail
LOD算法可以减少对于场景贡献较少的物体的复杂度。例如，一个物体距离视点较远的时候使用较低分辨率的纹理和更简单的几何结构，相对于视点较近的时候。LOD算法包含以下三个重要的部分：
* **给模型生成版本（Generation）**：简单版本的模型相对于原始模式由较少的三角面片构成。同时，简单版本的模型在渲染的时候，也可以使用更简单的着色器和纹理及更少的渲染遍数。
* **模型版本的选择标准（Selection）**：视点距离物体的距离。
* **模型版本之间的切换（Switching）**：切换的主要目的是为了避免Poping现象（在模型切换时，一种明显突变现象）。

#### DLOD(Discrete Level of Detail)
DLOD是最简单的LOD算法，其实现就是为模型创建不同细节程度的独立版本，将其应用到地形上的时候，DLOD意味着整个地形数据具有不同细节的层次结构，在渲染一帧的时候选择某一层的数据进行渲染。DLOD算法不适合渲染远近同时存在的场景，如果使用高细节的层，对于远处的场景使用过多的三角面片；而如果使用低细节的层，又没有足够的三角面片表现近处的物体。

#### CLOD(Continuous Level of Detail)
在CLOD算法中，场景的远景会选择不同细节层次的数据进行渲染。

### 2.2 Screen-Space Error
不管我们使用什么类型的LOD算法，我们必须给场景中某渲染物体选择一个具体的LOD层次。

### 2.3 Artifacts
虽然有很多用于渲染地形的LOD技术，但在这些LOD技术使用的时候会产生很多影响渲染结果的现象：
* **Cracking**：Cracking现象发生在LOD层之间交界处，高细节层的顶点没有正确地放置到低细节层对应的边缘处（距离边缘还有一定距离），这样就导致了网格间存在一定裂缝。解决该问题最直接的方法是在每一层的边缘处放置Skirts，但Skirts方法同样会引起一些新的问题。另外一种方法是强制将高细节层次的顶点移到粗糙层次的边缘处，Geometry-clipmapping LOD算法就是使用的这种方式，但是，这种方式引起了一个新的问题：T-junctions
* **T-junctions**：T-junctions问题和Cracking类似，但该问题更加隐蔽。与Cracking不同的是，发生T-junctions问题的高细节层的顶点确实放置到了低细节层的边缘处，形成一个T字形。但是在邻近的三角形光栅化过程中，浮点舍入的微小差异会导致地形表面上的微小针孔。
* **Popping**：在视点移动的时候，LOD层的突然切换可能照成这种问题，观察者可能会注意到一些顶点突然改变位置。