# 智能指针

## 1. scoped_ptr & scoped_array
scoped_ptr和scoped_array智能指针和它的名字**scoped**一样，
```C++
boost::scoped_ptr<int>   pInt(new int);
boost::scoped_array<int> pArray(new int[2]);
```
