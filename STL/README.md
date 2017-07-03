# STL

### 序列式容器
 - [vector](STL/vector.md)
 - [list](STL/list.md)
 - deque
 - [stack&queue](STL/stack_queue.md)
 - [heap ](STL/heap.md)：不属于STL组件，用作**priority queue**的底层实现机制。heap是一棵完全二叉树，分为max-heap和min-heap，STL heap是max-heap。heap的push使用上溯操作插入新节点，pop使用下溯操作弹出根节点。heap sort不断使用pop操作即可完成。
 - **priority queue**：使用heap作为其底层实现机制，每次得到queue中优先级最高的原素，直接使用heap的pop即可。
 
### 关联式容器
 - [BST](STL/BST.md)
 - [AVL Tree](STL/BST_AVL_TREE.md)
 - [RB Tree(TODO)](STL/BST_RB_TREE.md)
