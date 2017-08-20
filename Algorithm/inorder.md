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
		while (pNode)
		{
			Stack.push(pNode);
			pNode = pNode->left;
		}

		pNode = Stack.top();
		Stack.pop();

		Result.push_back(pNode);
		pNode = pNode->right;
	}

	return Result;
}
``
