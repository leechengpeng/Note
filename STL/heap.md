## heap
### VS2015 STL heap
```C++
#include <iostream>
#include <algorithm>
#include <vector>

template <typename T>
std::ostream& operator<<(std::ostream& vOut, const std::vector<T>& vContainers)
{
	for (auto Element : vContainers)
	{
		vOut << Element << " ";
	}

	return vOut;
}

int main()
{
	std::vector<int> Heap = { 29, 23, 20, 22, 17, 15, 26, 51, 19, 12, 35, 40 };

	std::make_heap(Heap.begin(), Heap.end());
	std::cout << Heap << std::endl;

	Heap.push_back(70);
	std::push_heap(Heap.begin(), Heap.end());
	std::cout << Heap << std::endl;

	std::pop_heap(Heap.begin(), Heap.end());
	// 必须pop_back后才能sort，否则将会报invalid heap的错误
	Heap.pop_back();
	std::cout << Heap << std::endl;

	std::sort_heap(Heap.begin(), Heap.end());
	std::cout << Heap << std::endl;

	return 0;
}
```
