* [二叉搜索树](#二叉搜索树binary-search-tree-bst)
* [平衡二叉搜索树](#平衡二叉搜索树balance-bst)

## 二叉搜索树（Binary Search Tree, BST）
### 特点
二叉搜索树，也称有序二叉树，是指一棵树具有以下特点：
* 若任意结点N的左子树不为空，则左子树上所有的结点的值均小于N的值
* 若任意结点N的右子树不为空，其右子树上所有的结点的值均大于N的值
* 任意结点N的左右子树都是一棵BST
* 没有键值相等的结点（结点间的值不可能相等）
* 沿着BST左结点一直检索可以得到最小值，沿着BST右结点一直检索可以得到最大值。
### 插入与删除
BST的插入比较简单，按照BST的特点即可快速插入新的结点。删除操作稍微复杂一点，分为以下两种情况（参照STL源码剖析P202图5-6a和5-6b）：
1. 待删除结点M只有一个子结点/子树N，此时直接将N结点连接到M的父节点上即可
2. 待删除结点M有左右子结点/子树，此时将结点M**右子树的最小值**（该最小值大于M左子树的任意值，同时也小于其右子树的任意值）替换结点M即可

### 实现
```C++
namespace LLL
{
	template <typename T>
	inline void safe_delete(T*& vPtr)
	{
		delete vPtr;
		vPtr = nullptr;
	}

	template <typename T>
	struct SNode
	{
		T m_Obj;
		SNode<T>* m_pLeft;
		SNode<T>* m_pRight;

		SNode(T vObj = T()) : m_Obj(vObj), m_pLeft(nullptr), m_pRight(nullptr) {}
		~SNode()
		{
			safe_delete(m_pLeft);
			safe_delete(m_pRight);
		}
	};

	template <typename T>
	class CBST
	{
	public:
		CBST() : m_pRoot() {}
		~CBST() { safe_delete(m_pRoot); }

		void insert(const T vObj);
		bool search(const T vObj) const;

		T maxmum()  const;
		T minimum() const;

	private:
		SNode<T>* m_pRoot;

		const SNode<T>* __maxmum(SNode<T>* vpNode)  const;
		const SNode<T>* __minimum(SNode<T>* vpNode) const;
	};

	// ***************************************************
	// FUNCTION:
	template <typename T>
	void CBST<T>::insert(const T vObj)
	{
		if (m_pRoot)
		{
			SNode<T>* pNode = m_pRoot;

			for (;;)
			{
				if (pNode->m_Obj > vObj)
				{
					if (pNode->m_pLeft == nullptr)
					{
						pNode->m_pLeft = new SNode<T>(vObj);
						break;
					}

					pNode = pNode->m_pLeft;
				}
				else if (pNode->m_Obj < vObj)
				{
					if (pNode->m_pRight == nullptr)
					{
						pNode->m_pRight = new SNode<T>(vObj);
						break;
					}

					pNode = pNode->m_pRight;
				}
				else
				{
					break;
				}
			}

		}
		else
		{
			m_pRoot = new SNode<T>(vObj);
		}
	}

	// ***************************************************
	// FUNCTION:
	template <typename T>
	bool CBST<T>::search(const T vObj) const
	{
		auto IsExist = false;
		for (auto pNode = m_pRoot; pNode != nullptr;)
		{
			if (pNode->m_Obj > vObj)
			{
				pNode = pNode->m_pLeft;
			}
			else if (pNode->m_Obj < vObj)
			{
				pNode = pNode->m_pRight;
			}
			else
			{
				IsExist = true;
				break;
			}
		}

		return IsExist;
	}

	// ***************************************************
	// FUNCTION:
	template <typename T>
	const SNode<T>* CBST<T>::__maxmum(SNode<T>* vpNode) const
	{
		while (vpNode->m_pRight) { vpNode = vpNode->m_pRight; }

		return vpNode;
	}

	// ***************************************************
	// FUNCTION:
	template <typename T>
	const SNode<T>* CBST<T>::__minimum(SNode<T>* vpNode) const
	{
		while (vpNode->m_pLeft) { vpNode = vpNode->m_pLeft; }

		return vpNode;
	}

	// ***************************************************
	// FUNCTION:
	template <typename T>
	inline T CBST<T>::maxmum() const
	{
		_ASSERT(m_pRoot);

		return __maxmum(m_pRoot)->m_Obj;
	}

	// ***************************************************
	// FUNCTION:
	template <typename T>
	T CBST<T>::minimum() const
	{
		_ASSERT(m_pRoot);

		return __minimum(m_pRoot)->m_Obj;
	}
}
```
### 测试
```C++
//FUNCTION: detect the memory leak in DEBUG mode
void installMemoryLeakDetector()
{
	// Enable run-time memory check for debug builds.
#if defined(DEBUG) | defined(_DEBUG)
	//_CRTDBG_LEAK_CHECK_DF: Perform automatic leak checking at program exit through a call to _CrtDumpMemoryLeaks and generate an error 
	//report if the application failed to free all the memory it allocated. OFF: Do not automatically perform leak checking at program exit.
	_CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);

	//the following statement is used to trigger a breakpoint when memory leak happens
	//comment it out if there is no memory leak report;
	//_crtBreakAlloc = 30372;
#endif
}

int main()
{
	installMemoryLeakDetector();

	LLL::CBST<int> BST;
	BST.insert(15);
	BST.insert(1);
	BST.insert(22);
	BST.insert(-2);
	BST.insert(15);
	BST.insert(12);
	BST.insert(25);

	std::cout << "Maxmux: " << BST.maxmum() << ", Minimum: "<< BST.minimum() <<std::endl;

	std::map<int, bool> Tests = { 
		// Exist
		{ 15, true }, { 1, true }, { 22, true }, { -2, true }, { 12, true }, { 25, true },
		// Does not exist 
		{-50, false}, { -5, false }, {-1, false}, {0, false}, { 5, false }, { 16, false }, { 100, false },
	};
	for (auto Test : Tests) { _ASSERT(BST.search(Test.first) == Test.second); }

	return 0;
}
```

## 平衡二叉搜索树（Balance BST）
由于输入数据不够随机或者插入删除操作，常常会导致BST不够平衡（平衡：没有任意结点的深度相对其他结点过深），而不平衡的BST会对搜索效率造成极大的影响。

根据不同的平衡条件实现方式，大概有如下几种平衡二叉搜索树：
* [AVL tree](BST_AVL_TREE.md)
* [RB tree](BST_RB_TREE.md)
* AA tree
