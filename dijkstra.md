# 迪杰斯特拉（Dijkstra）单源最短路径算法
### 算法流程
1. 创建源顶点到其他顶点的距离表**DistanceTable**并初始化：起始点距离为0，其他点距离为无限远
2. 选择**DistanceTable**中未标记的**最短距离顶点S**，然后将其标记为已访问
3. 更新**DistanceTable**中**点S**与其所有邻接顶点得距离值（邻接顶点未被访问过且当前距离小于之前距离才更新）
4. 循环流程2和流程3直至取不出最短距离点：**所有点都被标记**或者**当前最短距离为无限远**

### 例题：营救公主
Jesse是一个卓越的剑客，他英俊潇洒，而且机智过人。突然有一天，Jesse心爱的公主被魔王困在了一个巨大的迷宫中。Jesse听说这个消息已经是两天以后了，他知道公主在迷宫中还能坚持T天，他急忙赶到迷宫，开始到处寻找公主的下落。提供迷宫的地图以及所剩的时间T，请判断Jesse是否能救出心爱的公主。

输入数据以三个整数N，M，T开头，分别代表迷宫的长和高和公主能坚持的天数。紧接着有M行，N列字符，由"."，"\*"，"P"，"S"组成。其中"." 代表能够行走的空地。"\*" 代表墙壁，Jesse不能从此通过。"P" 是公主所在的位置。"S" 是Jesse的起始位置。每个时间段里Jesse只能选择“上、下、左、右”任意一方向走一步。成功救出返回“true”，失败则返回“false”。

#### 输入示例：
N = 4，M = 4，T = 10
```
.  .  .  .
.  .  .  .
.  .  .  .
S  *  *  P
```
#### 输出示例：true

### 实现
#### DistanceTable的设计
```C++
	class CDistanceTable
	{
	public:
		CDistanceTable(unsigned vNumVertices);
		CDistanceTable(unsigned vNumVertices, unsigned vSourceIndex);
		~CDistanceTable();

		void setSource(unsigned vSourceIndex);
		void setDistance(unsigned vIndex, unsigned vDistance);

		unsigned getDistanceToSource(unsigned vIndex) const;
		bool isVisited(unsigned vIndex) const;

	private:
		struct VertexAttr
		{
			VertexAttr() : Visited(false), DistanceToSource(UINT_MAX) {}

			bool     Visited;
			unsigned DistanceToSource;
		};

		unsigned    m_NumVertices;
		VertexAttr* m_Table;

		friend unsigned getMinDistanceIndexAndSetVisited(const CDistanceTable& vDT);
	};

	CDistanceTable::CDistanceTable(unsigned vNumVertices) : m_NumVertices(vNumVertices), m_Table(new VertexAttr[m_NumVertices]())
	{
		
	}

	CDistanceTable::CDistanceTable(unsigned vNumVertices, unsigned vSourceIndex) : CDistanceTable(vNumVertices)
	{
		setSource(vSourceIndex);
	}

	CDistanceTable::~CDistanceTable()
	{
		delete[] m_Table;
		m_Table = nullptr;
	}

	void CDistanceTable::setSource(unsigned vSourceIndex)
	{
		_ASSERT(vSourceIndex < m_NumVertices);
		m_Table[vSourceIndex].DistanceToSource = 0;
	}

	void CDistanceTable::setDistance(unsigned vIndex, unsigned vDistance)
	{
		_ASSERT(vIndex < m_NumVertices);
		if (vDistance < m_Table[vIndex].DistanceToSource) m_Table[vIndex].DistanceToSource = vDistance;
	}

	unsigned CDistanceTable::getDistanceToSource(unsigned vIndex) const
	{
		_ASSERT(vIndex < m_NumVertices);
		return m_Table[vIndex].DistanceToSource;
	}

	bool CDistanceTable::isVisited(unsigned vIndex) const
	{
		_ASSERT(vIndex < m_NumVertices);
		return m_Table[vIndex].Visited;
	}

	unsigned getMinDistanceIndexAndSetVisited(const CDistanceTable& vDT)
	{
		unsigned MinIndex = -1;
		unsigned MinDistance = UINT_MAX;

		for (unsigned i = 0; i < vDT.m_NumVertices; ++i)
		{
			if (!vDT.m_Table[i].Visited && vDT.m_Table[i].DistanceToSource < MinDistance)
			{
				MinIndex = i;
				MinDistance = vDT.m_Table[i].DistanceToSource;
			}
		}

		if (MinIndex != -1) { vDT.m_Table[MinIndex].Visited = true; }

		return MinIndex;
	}
```



> 参考：[Dijkstra 单源最短路径算法](http://www.cnblogs.com/gaochundong/p/dijkstra_algorithm.html)
