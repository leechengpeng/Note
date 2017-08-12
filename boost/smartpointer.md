# 智能指针

## 1. scoped_ptr & scoped_array

```C++
boost::scoped_ptr<int>   pInt(new int);
boost::scoped_array<int> pArray(new int[2]);
```
