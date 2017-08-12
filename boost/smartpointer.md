# 智能指针

## 1. scoped_ptr & scoped_array
**scoped**表示这两个指针都只能在**“局部范围”**使用，不能赋值可以避免**无意间**的指针传递
```C++
boost::scoped_ptr<int>   pInt(new int);
boost::scoped_array<int> pArray(new int[2]);
```
