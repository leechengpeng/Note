## 1. stack

stack是一种先进后出的数据结构，所有操作都是在栈顶进行。除了栈顶外，没有任何方法可以访问到stack的其他元素（That is to say：没有遍历操作）。

### 实现
SGI STL以deque作为stack的缺省实现方式。stack这种以其他容器作为底层实现方式的方法，实际上叫做适配器（Adapter），因此，STL stack并不被归类为容器，而是被归类为容器适配器（Container Adapter）。

```C++
std::stack<int> IStack;			// 默认底层容器为std::deque<int>
std::stack<int, std::list<int>> IStack;	// 以std::list<int>作为stack的低层容器
```

## 2. queue
