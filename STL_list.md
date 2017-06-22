# list

### 1. STL list是一个环状双向链表
只需要一个指针，便可以表示整个环状双向链表
```
link_type node;
```
node指向链表尾部，尾部结点是一个空白结点（没有存储实际值的结点）
```
iterator begin() { return (link_type)(node->next); }
iterator end()   { return node;}
```

### 2. iterator
* list是双向链表，便于iterator的前移和后移设计
* list的插入和删除操作不会造成list迭代器失效

