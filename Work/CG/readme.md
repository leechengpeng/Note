# 图形学

## 1. 基本概念
 - [齐次坐标](homogeneous_coordinate.md)
 - 通过三个互相垂直的向量可以定义一个坐标空间，并将这三个向量构造成一个矩阵，通过该矩阵可以让任意坐标变换到这个坐标空间中。

## 2. 模板测试
**模版测试**在**Fragment Shader**之后运行，通过掩码`0XFF`或`0X00`设置**模板缓存区**的值。

## 3. 深度测试
但**深度测试被启用**（默认关闭）的时候，OpenGL会将一个片段的深度值和深度缓冲区的内容进行对比：
* 如果**通过深度测试**，深度缓冲区更新相应深度值
* 如果**深度测试失败**，则相应片段会被丢弃

**深度测试**在**模板测试**之后，目前也有很多CPU支持**提前深度测试**，也就是说深度测试可以在片段着色器之后，这样性能上有比较大的提升。

**深度值是非线性的，离近平面的Z值进度较高（变化较快），离远平面较近的Z值变换较缓**，这种非线性的Z值计算已经融入到**投影矩阵**中，无需额外计算。

**深度冲突**：两个互相平行且非常紧密的排列在一起的平面，由于深度缓冲没有足够的精度判断哪个平面在前哪个平面在后，从而形成很多奇怪的花纹。这种现象在远平面附近尤为明显。通常有三种方式避免这种现象：
1. 永远不要把多个物体摆得太靠近，以至于它们的一些三角形会重叠
2. 使用更高精度的深度缓冲（32位替换24位）
3. 尽可能将近平面设置远一些

## 4. 面剔除（光栅化阶段之前，几何着色器之后进行）
**面剔除**的意思是指，剔除掉看不到的面，从而提高渲染效率。通常OpenGL通过**顶点的顺序**来确定顶点所在的面是**正面**还是**反面**，默认情况顶点逆时针顺序表示正面。例子：如果互相平行的两个三角面，都按照逆时针定义的顶点，视点观察到的第一个三角面由于其是逆时针定义的顶点，因此为正面，而后面一个三角面，在视点观察的情况下，已经变为顺时针，因此为反面而不用渲染。通常，**把所有三角的顶点都定义为逆时针是一个很好的习惯**。

## 5. Gamma校正
**Gamma**也叫灰度系数，每种设备都有自己的Gamma系数，通常不等于1（线性状态）。

**Gamma校正流程**：
> 将最终的颜色输出到应用上显示器Gamma的倒数。

**为什么需要Gamma校正**：因为程序中设置的颜色值，都是基于Gamma值为1的线性变化。由于显示器Gamma值不一样，其显示出来的结果和预期的结果就不一致了。因此需要通过**Gamma校正**将颜色值变换到预期结果。

**Gamma校正方式**：
* 通过OpenGL状态机打开`glEnable(GL_FRAMEBUFFER_SRGB);`，让OpenGL自动为你进行Gamma校正（在真缓冲区中自动执行）。
* 在着色器中手动执行校正过程：
```C
float gamma = 2.2;
fragColor.rgb = pow(fragColor.rgb, vec3(1.0/gamma)); // gamma的倒数
```

## 6. Blinn-Phong和Phong光照模型
**Blinn-Phong**光照模型是在**Phong**光照模型基础上添加添加了一个**半程向量**（halfway vector），当**半程向量**与**法向量**夹角越小，其镜面反射强度越高。**Blinn-Phong**光照模型主要解决**Phong**光照模型中，**视线与反射线夹角大于90而导致镜面反射强度为0的情况**。

## 7. 延迟着色
延迟着色流程如下：
1. 将场景集合信息渲染到G-buffer中
2. 绘制一个四边形，正常着色

延迟作色有两个明显的缺点：
1. 延迟作色的机制导致没法进行颜色混合
2. 基本所有的场景都只能使用同一种光照算法，不过可以通过材质来减轻这一问题

## 8. 延迟渲染 & 正向渲染
延迟渲染虽然**提高了渲染效率**，但却增加了**内存开销**，相当于也是一种以时间换空间的算法。当在一个比较小的场景，光源也比较小的情况下，延迟作色并不比正向渲染块。

## 9. 光照模型
光照模型有以下几种基本类型：
1. **Lambert模型**：理想漫反射，各个方向反射一致，能很好的表现**粗糙表面的光照**，`Diffuse = I * (L · N)`。
2. **Phong模型**：在**Lambert模型**基础上增加高光效果。
3. **Blinn-Phong**：参照6

## 10. 法线贴图
有两种法线贴图方式：
1. 将法线向量从TBN（切线）空间变换到世界世界坐标
2. 将**视线**和**光线**通过**TBN矩阵**转换到**切线空间**。其中，需要先计算**TBN矩阵**的**逆矩阵**，因为**TBN矩阵**是**正交矩阵**，因此**TBN矩阵的转置矩阵即是其逆矩阵**。

采用**方法二**的原因是：因为不是每一个Fragment的视线和光线都 因此可以将其放置到顶点着色器中进行，效率会得到很大的提升。而方法一中的每一个Fragment所对应的法向量都不相同，因此必须在Fragment Shader中进行。

## 11. 平移变换为什么要使用四维矩阵
1. 区别**点**和**向量**
2. 和旋转缩放变换统一，因为旋转和缩放只需要三维矩阵，而平移必须四维矩阵。三维矩阵没法进行平移变换，也就是说平移变换必须使用**矩阵加法**操作，而缩放和旋转使用**矩阵乘法**，如果多一个维度，平移也可以使用矩阵乘法进行变换了。

## 参考
1. [《Real-Time Rendering 3rd》 提炼总结](http://my.csdn.net/zhmxy555)