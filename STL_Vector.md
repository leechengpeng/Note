# vector

### iterator
vector<T>的迭代器实际上是其类型T的指针，定义如下：
```
typedef T* iterator;
```
因为vector维护的是线性空间，普通指针即可满足迭代器的所有操作：*、->、++、--...
