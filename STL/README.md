# STL

### 序列式容器
 - [vector](vector.md)
 - [list](list.md)
 - [deque](deque.md)
 - **stack&queue**：stack是一种**先进后出**的数据结构，而queue则是一种**先进先出**的数据结构。stack&queue都是以其他容器作为低层实现，这种方法叫做适配器（Adapter），因此，STL stack&queue并不被归类为容器，而是被归类为容器适配器（Container Adapter）。两种容器都只对其一端进行操作，因此，它们都没有遍历操作（没有迭代器）。
```C++
// 注：stack只对其一端进行操作，因此还可以使用std::vector<int>作为其低层实现。
std::stack<int, std::deque<int>> IStack; // 默认
std::stack<int, std::list<int>>  IStack; // 以std::list<int>作为stack的低层容器
```

 - [heap ](heap.md)：一棵完全二叉树，直接使用vector表示树形关系，特点是父节点都大于其子节点（max-heap）。heap的push使用上溯操作（将新节点放于vector末尾，新节点与父节点比较，如果大于父节点就与其交换，直至不需要交换或者到达根节点）插入新节点，pop使用下溯操作（将堆顶与vector末尾元素交换，然后堆顶节点与其较大子节点交换，直至叶子节点）弹出根节点。heap sort不断使用pop操作即可完成。
 - **priority queue**：使用heap作为其底层实现机制，每次得到queue中优先级最高的原素，直接使用heap的pop即可。
 
### 关联式容器
##### 1. 基于BST的关联式容器
- [BST](BST.md)
- [AVL-Tree](BST_AVL_TREE.md)
- [RB-Tree](BST_RB_TREE.md)
- **set**：
    * set的元素值就是键值，其所有元素都会根据其键值自动被排序（中序遍历：左根右，前序：根左右，后序：左右根）
    * 由于set元素值就是键值，也不能通过set的迭代器改变set的元素值，删除set的键值将会严重影响set的结构
    * set的删除和插入操作不会让其迭代器失效
- **map**：
    * map的元素值是Key和Value对，其中Key的值不允许修改（会破坏RB的结构），Value的值可以随意修改
    * map的删除和插入操作同set一样也不会让其迭代器失效  
- **multiset**：multiset调用RB-tree的insert_equal()，让其可以插入重复元素
- **multimap**：multimap调用RB-tree的insert_equal()，让其可以插入重复元素
    
##### 2. 基于Hashtable的关联式容器
基于[hash table](hashtable.md)实现的容器有：hash_set、hash_map、hash_multiset和hash_multimap，其用法和基于RB-Tree的容器基本相同，但hash的关联式容器没有排序的功能（即使看似排序，也是因为散列表足够大，数据均匀分布在hashtable中，造成数据貌似排过序的假象）。
