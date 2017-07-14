# 内存池

C++的内存管理比较复杂，管理不当可能造成如下问题：
* 内存碎片
* 内存泄漏

使用内存池技术，代替普通的内存分配与回收机制，有如下优势：
* 减少了申请/释放（new/delete）的操作，提高了内存管理的效率
* 能有效的减少内存碎片的数量
* 避免内存泄漏

### STL内存池的实现
SGI STL的内存池分为一级配置器和二级配置器：
1. **一级配置器**主要负责分配大于128字节的内存，内部直接以malloc/free实现
2. **二级配置器**使用**free_list**来管理小块内存，最小分配单位8字节：

|free_list index|内存大小|
|---|---|
| free_list[0] | 8 bytes|
| free_list[1] | 16 bytes|
| free_list[2] | 24 bytes|
| ... | ...|
| free_list[15] | 128 bytes|

STL内存池的基本工作流程如下：
1. 外部调用**配置器allocate**向内存池申请内存
2. 内存大于128bytes使用一级配置器
3. 内存小于128bytes使用二级配置器
    * allocate通过内存对齐的方式（申请6bytes，实际分配8bytes）在**free_list**中找到合适的内存块表头
    * 内存块表头不为空，则直接返回那块内存地址，并从内存表中移除这块地址
    * 内存块表头为空，重新分配20个此规格的内存空间链表


> 参考：[http://www.cnblogs.com/Creator/archive/2012/04/11/2430592.html](http://www.cnblogs.com/Creator/archive/2012/04/11/2430592.html)

