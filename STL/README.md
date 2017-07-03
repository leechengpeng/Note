# STL

### 序列式容器
 - [vector](vector.md)
 - [list](list.md)
 - deque
 - **stack&queue**：stack是一种**先进后出**的数据结构，而queue则是一种**先进先出**的数据结构。stack&queue都是以其他容器作为低层实现，这种方法叫做适配器（Adapter），因此，STL stack&queue并不被归类为容器，而是被归类为容器适配器（Container Adapter）。
```C++
// 注：stack只对一端进行操作，因此还可以使用std::vector<int>作为其低层实现。
std::stack<int, std::deque<int>> IStack; // 默认
std::stack<int, std::list<int>>  IStack; // 以std::list<int>作为stack的低层容器
```

 - [heap ](heap.md)：不属于STL组件，用作**priority queue**的底层实现机制。heap是一棵完全二叉树，分为max-heap和min-heap，STL heap是max-heap。heap的push使用上溯操作插入新节点，pop使用下溯操作弹出根节点。heap sort不断使用pop操作即可完成。
 - **priority queue**：使用heap作为其底层实现机制，每次得到queue中优先级最高的原素，直接使用heap的pop即可。
 
### 关联式容器
 - [BST](BST.md)
 - [AVL Tree](BST_AVL_TREE.md)
 - [RB Tree(TODO)](BST_RB_TREE.md)
