# deque
vector是单向开口的连续线性空间，deque是一种双向开口的连续线性空间。即：deque可以在头尾两端做插入与删除操作，虽然vector也可以实现类似的前端插入操作，但效率会异常低。

### 存储策略
deque数据结构中，设计了一个指针数组**map**（类型为T\*\*），map中存放了一系列指针，指向真实存放数据的区域（一大块连续的数据区域），称为缓冲区。**SGI STL**可以指定缓冲区大小，默认值0表示512bytes的缓冲区。

### 迭代器
deque的迭代器属于Random Access Iterator，但它不是像vector那样简单的迭代器，设计较为复杂：
```C++
struct __deque_iterator
{
	T* cur;           // 指向当前具体的元素
	T* first;         // 指向当前缓冲区的头部
	T* last;          // 指向当前缓冲区的尾部
	map_pointer node; // 指向当前迭代器所在map的位置
};
```

deque迭代器设计比较复杂，导致其相关的运算效率较低。因此，对于可变长度的数列应尽量使用vector。例如，在对deque进行排序时，为了提高效率，可以先将数据放入vector中排好序过后，然后再放回deque中。
