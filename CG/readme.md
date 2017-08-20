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

## 4. 面剔除（光栅化阶段进行）
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