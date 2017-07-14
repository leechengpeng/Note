# 内存池

C++的内存管理比较复杂，管理不当可能造成如下问题：
* 内存碎片
* 内存泄漏

使用内存池技术，代替普通的内存分配与回收机制，有如下优势：
* 减少了申请/释放（new/delete）的操作，提高了内存管理的效率
* 能有效的减少内存碎片的数量
* 避免内存泄漏

### STL内存池的实现
SGI STL的内存池分为一级配置器和二级配置器：
1. **一级配置器**主要负责分配大于128字节的内存，内部直接以malloc/free实现
2. **二级配置器**使用**free_list**来管理小块内存，最小分配单位8字节：

|free_list index|内存大小|
|---|---|
| free_list[0] | 8 bytes|
| free_list[1] | 16 bytes|
| free_list[2] | 24 bytes|
| ... | ...|
| free_list[15] | 128 bytes|

STL内存池的基本工作流程如下：
1. 外部调用**配置器allocate**向内存池申请内存
2. 内存大于128bytes使用**一级配置器**
3. 内存小于128bytes使用**二级配置器**，详细请阅读代码`void* CMemoryPool::allocate(unsigned vBytes)`
    * allocate通过**内存对齐**的方式（15bytes，实际分配16bytes）在**free_list**中找到合适起点位置free_list[x]，x范围为[0, 15]
    * \*free_list[x]链表头不为NULL，则直接返回链表头那块地址，并将这块内存移除表头
    * 内存块表头为NULL，重新额外分配20块此规格的内存空间链表

```C++
void* CMemoryPool::allocate(unsigned vBytes)
{
	// 一级配置器
	if (vBytes > m_MaxBytes) { return malloc(vBytes); }

	// 二级配置器
	obj** pFreeList = free_list + _FREELIST_INDEX(vBytes);
	obj* pMemory = *pFreeList;
	
	// 直接返回链表头地址，并移除这块地址
	if (pMemory != nullptr)
	{
		*pFreeList = pMemory->free_list_link;
	}
	// 请求分配20块此规格空间，并返回头部地址
	else
	{
		pMemory = reinterpret_cast<obj*>(_refill(_ROUND_UP(vBytes)));
	}

	return pMemory;
}
```

### 申请额外空间
内存分配策略如下：
1. 内存满足**需求量**（需求量指：本身客户端申请 + 额外预留空间 = 20），就直接将内存块从内存池中抽出
2. 如果内存无法满足**需求量**，但满足客户端本身申请的一块空间，那就不重新分配空间，直接返回那块空间
3. 无法满足1和2的情况，内存池则需要重新分配空间：
    * 如果内存池还有部分剩余空间，则将剩余空间调整到合适的free_list中
    * 调用malloc分配内存，补充内存池，并调整内存池的起始点

```C++
// **************************************************
// Function:
char* CMemoryPool::_chunk_alloc(unsigned vBytes, int& vNobjs)
{
	unsigned RequstBytes   = vBytes * vNobjs;
	unsigned MPRemainBytes = m_FreeEnd - m_FreeStart;

	char* pRequestMemory = nullptr;
	// 1. 内存池满足总需求量
	if (MPRemainBytes >= RequstBytes)
	{
		pRequestMemory = m_FreeStart;
		m_FreeStart += RequstBytes;
	}
	// 2. 内存池满足一个请求
	else if (MPRemainBytes >= vBytes)
	{
		// 计算内存池剩余空间足够分配的数目
		vNobjs = MPRemainBytes / vBytes;
		RequstBytes = MPRemainBytes;

		pRequestMemory = m_FreeStart;
		m_FreeStart += RequstBytes;
	}
	// 3. 内存池无法分配任何空间
	else
	{
		unsigned BytesToGet = 2 * RequstBytes + _ROUND_UP(m_PoolSize >> 4);

		if (MPRemainBytes > 0)
		{
			// 将剩余空间调整到合适的free_list中
			obj** pFreeList = free_list + _FREELIST_INDEX(MPRemainBytes);
			reinterpret_cast<obj*>(m_FreeStart)->free_list_link = *pFreeList;

			*pFreeList = reinterpret_cast<obj*>(m_FreeStart);
		}

		// 调用malloc分配内存，补充内存池
		m_FreeStart = reinterpret_cast<char*>(malloc(BytesToGet));

		// TODO：heap空间被分配完了，malloc失败

		m_PoolSize += BytesToGet;
		m_FreeEnd = m_FreeStart + m_PoolSize;

		return _chunk_alloc(vBytes, vNobjs);
	}

	return pRequestMemory;
}
```

### 分配额外空间放到对应的free_list中
将之前申请的额外空间，一块一块分配到free_list中形成内存链状结构：
```C++
// **************************************************
// Function:
void* CMemoryPool::_refill(unsigned vBytes)
{
	int AllocatedChunkSize = 20;
	char* pChunk = _chunk_alloc(vBytes, AllocatedChunkSize);

	// 从_chunk_alloc值或得一块区域，直接返回给调用者
	if (AllocatedChunkSize == 1) { return pChunk; }

	auto pFreeList = free_list + _FREELIST_INDEX(vBytes);

	auto pRequestMemory = reinterpret_cast<obj*>(pChunk);
	// 用_chunk_alloc分配来的大量区块配置对应大小的free_list
	auto pNextObj = *pFreeList = pRequestMemory + vBytes;
	
	// 将申请的额外空间放到对应的free_list链状结构中
	for (int i = 0; ; ++i)
	{
		auto pObj = pNextObj;
		pNextObj = reinterpret_cast<obj*>(reinterpret_cast<char*>(pNextObj) + vBytes);

		if (AllocatedChunkSize - 1 == i)
		{
			pObj->free_list_link = nullptr;
			break;
		}
		else
		{
			pObj->free_list_link = pNextObj;
		}
	}

	return pRequestMemory;
}
```

### 完整代码
```C++
#pragma once
#include <cstdlib>	// for malloc

class CMemoryPool
{
public:
	static CMemoryPool* getInstance();

	void* allocate(unsigned vBytes);

protected:
	CMemoryPool();
	~CMemoryPool();

	void* _refill(unsigned vBytes);
	char* _chunk_alloc(unsigned vBytes, int& vNobjs);

	// 保持和SGI_STL命名一致
	size_t _FREELIST_INDEX(unsigned vBytes);
	size_t _ROUND_UP(unsigned vBytes);

private:
	const unsigned m_Align;
	const unsigned m_MaxBytes;
	const unsigned m_NumFreeList;

	char* m_FreeStart;
	char* m_FreeEnd;
	unsigned m_PoolSize;

	union obj
	{
		union obj* free_list_link;
		char client_data[1];
	};
	obj** free_list;

	static CMemoryPool* m_Instance;
};

CMemoryPool* CMemoryPool::m_Instance = nullptr;

CMemoryPool::CMemoryPool() :m_Align(8), m_MaxBytes(128), m_NumFreeList(m_MaxBytes / m_Align), m_FreeStart(nullptr), m_FreeEnd(nullptr), m_PoolSize(0)
{
	/*
	free_list[0]:  8bytes
	free_list[1]:  16bytes
	free_list[2]:  24bytes
	...
	free_list[15]: 128bytes
	*/
	free_list = new obj*[m_NumFreeList]();	// obj* free_list[m_NumFreeList]
}

CMemoryPool::~CMemoryPool()
{
	delete[] free_list;

	free_list = nullptr;
}

// **************************************************
// Function:
CMemoryPool* CMemoryPool::getInstance()
{
	if (m_Instance == nullptr) { m_Instance = new CMemoryPool(); }
	return m_Instance;
}

// **************************************************
// Function:
void* CMemoryPool::allocate(unsigned vBytes)
{
	// 一级配置器
	if (vBytes > m_MaxBytes) { return malloc(vBytes); }

	// 二级配置器
	obj** pFreeList = free_list + _FREELIST_INDEX(vBytes);
	obj* pMemory = *pFreeList;
	
	// 直接返回链表头地址，并移除这块地址
	if (pMemory != nullptr)
	{
		*pFreeList = pMemory->free_list_link;
	}
	// 请求分配20块此规格空间，并返回头部地址
	else
	{
		pMemory = reinterpret_cast<obj*>(_refill(_ROUND_UP(vBytes)));
	}

	return pMemory;
}

// **************************************************
// Function:
inline size_t CMemoryPool::_FREELIST_INDEX(unsigned vBytes)
{
	return (vBytes + m_Align - 1) / m_Align - 1;
}

// **************************************************
// Function:
inline size_t CMemoryPool::_ROUND_UP(unsigned vBytes)
{
	return (vBytes +m_Align - 1) & ~(m_Align - 1);
}

// **************************************************
// Function:
void* CMemoryPool::_refill(unsigned vBytes)
{
	int AllocatedChunkSize = 20;
	char* pChunk = _chunk_alloc(vBytes, AllocatedChunkSize);

	// 从_chunk_alloc值或得一块区域，直接返回给调用者
	if (AllocatedChunkSize == 1) { return pChunk; }

	auto pFreeList = free_list + _FREELIST_INDEX(vBytes);

	auto pRequestMemory = reinterpret_cast<obj*>(pChunk);
	// 用_chunk_alloc分配来的大量区块配置对应大小的free_list
	auto pNextObj = *pFreeList = pRequestMemory + vBytes;

	// 将申请的额外空间放到对应的free_list链状结构中
	for (int i = 0; ; ++i)
	{
		auto pObj = pNextObj;
		pNextObj = reinterpret_cast<obj*>(reinterpret_cast<char*>(pNextObj) + vBytes);

		if (AllocatedChunkSize - 1 == i)
		{
			pObj->free_list_link = nullptr;
			break;
		}
		else
		{
			pObj->free_list_link = pNextObj;
		}
	}

	return pRequestMemory;
}

// **************************************************
// Function:
char* CMemoryPool::_chunk_alloc(unsigned vBytes, int& vNobjs)
{
	unsigned RequstBytes   = vBytes * vNobjs;
	unsigned MPRemainBytes = m_FreeEnd - m_FreeStart;

	char* pRequestMemory = nullptr;
	// 1. 内存池满足总需求量
	if (MPRemainBytes >= RequstBytes)
	{
		pRequestMemory = m_FreeStart;
		m_FreeStart += RequstBytes;
	}
	// 2. 内存池满足一个请求
	else if (MPRemainBytes >= vBytes)
	{
		// 计算内存池剩余空间足够分配的数目
		vNobjs = MPRemainBytes / vBytes;
		RequstBytes = MPRemainBytes;

		pRequestMemory = m_FreeStart;
		m_FreeStart += RequstBytes;
	}
	// 3. 内存池无法分配任何空间
	else
	{
		unsigned BytesToGet = 2 * RequstBytes + _ROUND_UP(m_PoolSize >> 4);

		if (MPRemainBytes > 0)
		{
			// 将剩余空间调整到合适的free_list中
			obj** pFreeList = free_list + _FREELIST_INDEX(MPRemainBytes);
			reinterpret_cast<obj*>(m_FreeStart)->free_list_link = *pFreeList;

			*pFreeList = reinterpret_cast<obj*>(m_FreeStart);
		}

		// 调用malloc分配内存，补充内存池
		m_FreeStart = reinterpret_cast<char*>(malloc(BytesToGet));

		// TODO：heap空间被分配完了，malloc失败

		m_PoolSize += BytesToGet;
		m_FreeEnd = m_FreeStart + m_PoolSize;

		return _chunk_alloc(vBytes, vNobjs);
	}

	return pRequestMemory;
}
```

> 参考：[http://www.cnblogs.com/Creator/archive/2012/04/11/2430592.html](http://www.cnblogs.com/Creator/archive/2012/04/11/2430592.html)

