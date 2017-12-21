# 海量数据处理
指基于海量数据的存储、处理或操作，通常因为数据量过大，导致无法短时间内迅速处理或者无法一次性载入内存。
## 海量数据处理方法
### 1. 散列分治
大文件散列成小文件（如果文件能直接载入内存，则不需要散列成小文件） -> 小文件通过hash_map统计 -> 堆排序或归并排序进行统计。

### 2. 位图（BitMap）
位图：用一个**bit**位代表**某个元素X**对应的值（value），而键就是该元素。采用bit位为单位存储数据，能有效的节省计算时的存储空间。位图算法需注意一下问题：
1. 一般通过数组的下标来表示元素，数组所对应的值来表示存在或者重复等
2. 在32位系统中，最大能分配的空间为2^32字节大小的空间。测试在VS32位项目中最多分配了1.6G左右的内存：`char* pBitMap = new char[1647483640];`，需注意的是栈上能分配的空间更少，大概为1M左右（栈代码结构限制）

```C++
// 置位操作
void setBit(char* vBitMap, size_t vIndex)
{
	static unsigned ByteSize = 8;

	vBitMap += vIndex / ByteSize;
	*vBitMap |= 0x01 << (vIndex % ByteSize);
}
```

#### 实例：排序
需要对范围在[0, 7]的5个(4, 7, 2, 5, 3)无重复元素进行排序，采用位图方法：
1. 开辟8块bit位空间：

| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |

2. 遍历这5个元素，分别放入所对应的位中

| 0 | 0 | 1 | 1 | 1 | 1 | 0 | 1 |
| --- | --- | --- | --- | --- | --- | --- | --- |

3. 遍历这块内存，将bit位值为1的元素的index输入，即为排序后的数据(2, 3, 4, 5, 7)


### 3. Tire树
Tire树，也叫字典树，优点在于最大限度的减小无畏的字符串比较。常用语统计和排序大量字符串的场景，如搜索引擎文本词频统计。它有以下三个特点：
1. 根节点不包含字符
2. 从根节点到某一节点路径上所有的字符串连接起来，即为该节点对应的字符串
3. 每个节点的所有子节点包含的字符都不相同
比如b, abc, abd, bcd, abcd, efg和hii这六个单词构成的Tire树如下：
1. **root**
2. **root->a，root->b，root->e，root->h**
3. **a->b，b->c，e->f，h->i** 
4. .....

## 海量数据处理实例
### 1. 海量日志数据，提取出某日访问百度次数最多的那个IP。
**方案一（哈希）**：IP的数目还是有限的，最多2^32个，所以可以考虑使用hash将ip直接存入内存，然后使用堆进行统计。

**方案二（散列分治）**：将访问百度的日志中的IP取出来，逐个写入到一个大文件中。然后使用模为1000，把整个大文件映射为1000个小文件，再找出每个小文中出现频率最大的IP（可以采用hash_map进行频率统计，然后再找出频率最大的几个）及相应的频率。然后再在这1000个最大的IP中，找出那个频率最大的IP。

### 2. 搜索引擎会通过日志文件把用户每次检索的字符串（1~255Bytes）都记录下来，目前有1000万条记录（不重复的大概300万），统计最频繁查询的10字符串，要求使用的内存不超过1GB。
这是一道典型的**Top K算法**，可采用以下两种方案：

**方案一**：hash_map + heap

**方案二**：利用Tire树，记录关键字在该查询串出现的次数，如果没有出现则记录为0。然后再使用最小堆（大小为10）来对现有频率排序。

### 3. 有一个1G大小的一个文件，里面每一行是一个词，词的大小不超过16字节，内存限制大小是1M。返回频数最高的100个词。
1. 顺序读文件中，对于每个词x，取hash(x)%5000，然后按照该值存到5000个小文件（记为x0,x1,...x4999）中。
2. 这样每个文件大概是200k左右。如果其中的有的文件超过了1M大小，还可以按照类似的方法继续往下分，直到分解得到的小文件的大小都不超过1M。 对每个小文件，统计每个文件中出现的词以及相应的频率（可以采用trie树/hash_map等），并取出出现频率最大的100个词（可以用含100个结点的最小堆），并把100个词及相应的频率存入文件，这样又得到了5000个文件。下一步就是把这5000个文件进行归并（类似与归并排序）的过程了。

### 4. 给定a、b两个文件，各存放50亿个url，每个url各占64字节，内存限制是4G，让你找出a、b文件共同的url？
1. 遍历文件a，对每个url求取hash(url)%1000，然后根据所取得的值将url分别存储到1000个小文件（记为a0,a1,...,a999）中。这样每个小文件的大约为300M。
2. 遍历文件b，采取和a相同的方式将url分别存储到1000小文件（记为b0,b1,...,b999）。
3. 这样处理后，所有可能相同的url都在对应的小文件（a0vsb0,a1vsb1,...,a999vsb999）中，不对应的小文件不可能有相同的url。然后我们只要求出1000对小文件中相同的url即可。
4. 求每对小文件中相同的url时，可以把其中一个小文件的url存储到hash_set中。然后遍历另一个小文件的每个url，看其是否在刚才构建的hash_set中，如果是，那么就是共同的url，存到文件里面就可以了。

### 5. 在2.5亿个整数中找出不重复的整数，注，内存不足以容纳这2.5亿个整数。
**方案一（BitMap）**：采用2-Bitmap（每个数分配2bit，00表示不存在，01表示出现一次，10表示多次，11无意义）进行，共需内存内存，还可以接受。然后扫描这2.5亿个整数，查看Bitmap中相对应位，如果是00变01，01变10，10保持不变。所描完事后，查看bitmap，把对应位是01的整数输出即可。

**方案二（散列）**：也可采用与第1题类似的方法，进行划分小文件的方法。然后在小文件中找出不重复的整数，并排序。然后再进行归并，注意去除重复的元素。

### 6. 给40亿个不重复的unsigned int的整数，没排过序的，然后再给一个数，如何快速判断这个数是否在那40亿个数当中？
**方案（BitMap）**：申请512M的内存，一个bit位代表一个unsigned int值。读入40亿个数，设置相应的bit位，读入要查询的数，查看相应bit位是否为1，为1表示存在，为0表示不存在。