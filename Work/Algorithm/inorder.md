# 二叉树的中序遍历
注：**BST的中序遍历是一个有序的数列，可用于判断是否是有效的二叉搜索树。**
```C++
std::vector<TreeNode*> inorderTraversal(TreeNode* vRoot)
{
	std::vector<TreeNode*> Result;

	TreeNode* pNode = vRoot;
	std::stack<TreeNode*> Stack;
	while (pNode || !Stack.empty())
	{
		// 将二叉树的左节点不断压入栈中
		while (pNode)
		{
			Stack.push(pNode);
			pNode = pNode->left;
		}

		// 弹出当前最左端子节点
		pNode = Stack.top();
		Stack.pop();
		
		// 保存遍历结果
		Result.push_back(pNode);
		// 如果右节点存在，访问其右节点
		pNode = pNode->right;
	}

	return Result;
}
``
