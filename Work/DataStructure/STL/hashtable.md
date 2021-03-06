# 哈希表（hash table）
哈希表也称散列表，它可以通过哈希函数的映射很快的定位到待查询的数据位置。这种数据结构在插入、删除、搜索等操作上通常具有“常数时间”的表现。

### 1. 哈希函数
**负载系数（overload factor）**：表示存储元素个数除以哈希表大小，范围为[0,1]
#### 为什么使用哈希函数？
哈希表常用数组表示。首先考虑如果不用哈希函数映射，可能会出现如下情况：只插入2和102400两个数据，这个时候数组的大小至少大于102400才能装下这两个数据（负载系数约等于零）。因此，可以通过某种方式把数据映射到一定范围，这种方式就叫哈希函数映射。如：将数据映射到[0,10)之间，此时就只需要大小10的数组即可装下这两个数据。如果插入的数据量超过10（即负载系数大于1），可以通过某种策略扩大数组，然后将原始重新调整到新的空间中。

### 2. 冲突解决
* 线性探测：可能产生primary clustering
* 二次探测：可能产生secondary clustering
* 开链：使用list（桶）保存冲突的元素
