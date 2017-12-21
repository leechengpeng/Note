# BOOST

# 1. 智能指针
## 1. scoped_ptr & scoped_array
**scoped**表示这两个指针都只能在**局部范围**使用，不能赋值可以避免**无意间**的指针传递
```C++
boost::scoped_ptr<int>   pInt(new int);
boost::scoped_array<int> pArray(new int[2]);
```
## 2. shared_ptr & shared_array
**shared_ptr**和**scoped_ptr**不同在于shared_ptr不会独占对象，也就是说可能有多个shared_ptr指向同一个对象。
```
boost::shared_ptr<int> pInt(new int);
boost::shared_ptr<int> pInt2(pInt);
```

# 2. 
