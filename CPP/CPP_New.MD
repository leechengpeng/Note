# C++的三种new

## 1. new operator

常用的new方法：

	int* pArray = new int[10]();

此处的**new operator**实际上完成了两个步骤：
1. 申请内存
2. 初始化对象

## 2. operator new
只负责申请内存，相当于**new operator**的第1步：

	int* pBuffer = static_cast<int*>(::operator new(sizeof(int)));
  
## 3. placement new
用于给指定内存进行初始化操作，即**new operator**的第2步：

	pBuffer = new(pBuffer) int(1024);

# 示例
```C++
#include <iostream>

class CClass
{
public:
	CClass() { std::cout << "Call constructor..." << std::endl; }
	~CClass() { std::cout << "Call deconstructor..." << std::endl; }
};

int main()
{
	CClass* pClass = (CClass*)malloc(sizeof(CClass));	// operator  new
	pClass = new(pClass) CClass();				// placement new

	free(pClass);		// operator  delete
	pClass->~CClass(); 	// placement delete

	return 0;
}
```
`注:C语言中的malloc和free相当于operator new和operator delete`
