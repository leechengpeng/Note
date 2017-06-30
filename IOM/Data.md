## C++ Data语意学

### 1. 类的大小
定义一个空的类`CAnimal`：
```C++
class CAnimal {};

std::cout << sizeof(CAnimal) << std::endl;  // VS2015: 1
```

空类`CAnimal`中虽然没有定义任何数据，但编译器会强制的给类`CAnimal`插入一个**char**（1 byte）型的数据。这种策略使得类`CAnimal`的对象在内存中都有自己独立的地址。
```C++
CAnimal Dog;
CAnimal Cat;

std::cout << "Dog: " << &Dog << std::endl;  // VS2015: 0x0058f833
std::cout << "Cat: " << &Cat << std::endl;  // VS2015: 0x0058f827
```
