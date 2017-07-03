## AVL Tree (Adelson-Velskii-Landis Tree)
AVL Tree是一个“加了额外平衡添加”的BST，AVL不能保证BST的深度为Log(N)，但它能保证BST中任意结点的左右子树深度相差最多为1。

如下图所示，将点11插如AVL-Tree中，此时灰色节点22和18违反了AVL-Tree的规则。只需要调整“插入点至根节点”路径上，平衡状态被破坏的节点中最深那个节点**X(18)**，便可以让整棵树重新保持平衡。具体流程如下图：

![non-AVL Tree](https://github.com/leechengpeng/Note/blob/master/Resources/Images/IMG_0003.PNG)

由于节点**X**最多拥有两个子节点，左右子节点的深度相差为2时就是不平衡状态。根据插入点的位置，将破坏平衡的情况分为四种：
1. 插入点位于**X**的左子节点的左子树：左左

![左左](https://github.com/leechengpeng/Note/blob/master/Resources/Images/IMG_0005.PNG)
2. 插入点位于**X**的左子节点的右子树：左右

![右右](https://github.com/leechengpeng/Note/blob/master/Resources/Images/IMG_0006.PNG)
3. 插入点位于**X**的右子节点的左子树：右左
4. 插入点位于**X**的右子节点的右子树：右右

根据上述四种不同情况，可以将其分为两类插入：
1. **外侧插入**：情况1和4，采用**单旋转**调整树形结构使之重新平衡
2. **内侧插入**：情况2和3，采用**双旋转**调整树形结构使之重新平衡

**单旋转**书P205，**双旋转**(两次单旋转)书P206。
