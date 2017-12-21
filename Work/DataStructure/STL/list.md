# list

### 1. STL list是一个环状双向链表
只需要一个指针，便可以表示整个环状双向链表
```
link_type node;
```
node指向链表尾部，尾部结点是一个空白结点（没有存储实际值的结点）
```
iterator begin() { return (link_type)(node->next); }
iterator end()   { return node; }
```
对于一个空链表，node的构造如下：
```
node->next = node;
node->pre  = node;
```

### 2. iterator
* list是双向链表，便于iterator的前移和后移设计
* list的插入和删除操作不会造成list迭代器失效

### 3. list不能使用STL算法的sort函数
list不能使用STL算法的sort函数，只能使用list自带的sort函数。因为STL算法sort函数采用的随机访问迭代器（可以随机访问任意位置元素），而list迭代器只提供了++和--操作。
