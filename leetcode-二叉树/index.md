# LeetCode 二叉树


总结一下今天刷二叉树遇到的难题和一些思路。

## 从前序与中序遍历序列构造二叉树
给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

### 思路(借鉴大神)
前序遍历 => （根节点，左子树前序序列，右子树前序序列）；
中序遍历 => （左子树中序序列，根节点，右子树中序序列）
并且各序列的长度相同

1. 先根据前序序列得到当前根节点的值(preorder[0]);
2.  申请当前树节点, 并初始化;
3.  如果序列长度为1, 说明这颗树只有根节点，直接返回这个节点；
4. 查找中序序列里当前节点值(没有重复元素)，找得到两子树的序列长度；
5. 将左子树前序序列, 右子树前序序列，左子树中序序列, 右子树中序序列创建出来；
6. 以左子树，右子树分类，进行递归, 构造左右子树( 回到步骤1 ), 将返回结果(子树根节点)赋值给左右指针；
7. 返回这个节点；

#### 代码实现过程C++

```
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
	  // 如果是一颗空树，直接返回空指针；
        if(!preorder.size())
            return nullptr;

	  // 申请节点并初始化
        TreeNode *root = new TreeNode();
        root->val = preorder[0];
        root->left = nullptr;
        root->right = nullptr;

	  // 如果这棵树只有根节点，直接返回这个节点；
        if(preorder.size() == 1)
            return root;

	  // 查找当前节点在中序序列中的位置；
        auto it = find(inorder.begin(), inorder.end(), preorder[0]);

	  // //创建左子树前序序列, 右子树前序序列，左子树中序序列, 右子树中序序列；
        vector<int> pl(preorder.begin() + 1,preorder.begin() + 1 + (it - inorder.begin()));
        vector<int>pr(preorder.begin() + 1 + (it - inorder.begin()), preorder.end());
        vector<int> il(inorder.begin(), it);
        vector<int> ir(it + 1, inorder.end());

	  // 创建左右子树, 并将它们的根节点赋值给当前节点的指针；
        root->left = buildTree(pl, il);
        root->right = buildTree(pr, ir);
        return root;
    }
};
```

这段代码的递归思路很nice，值得记录下来学习！

- 时间复杂度： O(N)，N是树中的节点个数；
- 空间复杂度： 在创建过程中，除了答案需要的O(N)空间之外，我们还创建了左右子树的先序、中序序列，所以中的空间复杂度为o(N)；


## 二叉树的最近公共祖先
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。

### 思路
使用后序遍历的模型，每个父节点都可以接收到两个孩子节点的状态，并把这个状态往上传递，直到该节点满足祖先节点的条件。

1. 对二叉树进行后序遍历，如果当前节点是p或者q或者NULL，则返回当前节点；
2. 对子节点进行遍历，获取左右孩子的状态；
3. 若左右孩子都返回了一个非空指针，则说明当前节点是他们的最近公共祖先，返回当前节点即可；若右孩子扎到了，则返回右孩子；若左孩子找到了，则返回左孩子；否则返回NULL；

#### 代码实现过程
```
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == p || root == q || root == NULL)
            return root;
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);

	  // left 和 right都不为空，说明此时root就是最近公共节点;
        if(left && right)
            return root;
	  // 如果left为空，right不为空，就返回right，说明目标节点是通过right返回的，反之亦然;
        if(!left)
            return right;
        return left;
    }
};
```

- 时间复杂度： 递归模型是后序遍历，最坏情况下需要遍历整棵树，故为O(N)；
- 空间复杂度： 递归调用栈的深度等于二叉树的高度，最坏情况下二叉树为一个链表，故为O(N)；


## 二叉树的右视图
给定一个二叉树的 根节点 root，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

### 思路
就是对二叉树进行层序遍历嘛，把每一层的最右个节点逐个加入到ans中即可。

- 时间复杂度： 遍历完整棵树，故为O(N)；
- 空间复杂度：使用了一个队列存储书中一层的节点，故为O(log<sub>2</sub>N);


## 二叉树的直径
给你一棵二叉树的根节点，返回该树的 直径 。二叉树的 直径 是指树中任意两个节点之间最长路径的 长度 。这条路径可能经过也可能不经过根节点 root 。两节点之间路径的 长度 由它们之间边数表示。

### 思路
easy，就是求树中节点左右孩子高度和的最大值，对求二叉树高度的代码稍加改造即可。

- 时间复杂度：O(N);
- 空间复杂度：递归栈的大小为log<sub>2</sub>N，最坏情况下，二叉树为链表，故为O(N)；


## 二叉树中的最大路径和
二叉树中的 路径 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。路径和 是路径中各节点值的总和。给你一个二叉树的根节点 root ，返回其 最大路径和 。

### 思路
1. 每遍历一个节点，都要求出包含该节点在内的最大路径，并且在后面的遍历中更新这个最大值。对于当前节点来说，它的最大路径和等于左右子树的最大路径加上本身的值。但是有个前提，如果左右孩子中的最大路径和小于或者等于0了，那就不需要把这个值加上去，因为它对最大路径和产生了负贡献，使得最大值变小了；
2. 对于一个节点来说，它对最大路径和的贡献来与源于其本身的值与其左右孩子中的最大值，所以在求左右子树最大值的时候，返回其节点值与其最大的孩子值即可；

#### 代码实现
```
class Solution {
public:
    int(getMax(TreeNode* root, int &ans)){
	  // 当前节点为空，其对最大路径和的贡献为0，故返回0；
        if(!root)
            return 0;

	  // 求左子树的最大贡献，若左子树是负贡献，则其为0；
        int left_max = max(getMax(root->left, ans), 0);

	  // 求右子树的最大贡献，若右子树是负贡献，则其为0；
        int right_max = max(getMax(root->right, ans), 0);

	  // 计算当前节点的最大路径和；
        int cur_max = root->val + left_max + right_max;

	  // 更新最大路径和
        ans = max(ans, cur_max);

	  // 返回当前节点对最大路径的贡献(当前节点值 + 左右孩子中的最大值)；
        return root->val + max(left_max, right_max);
    }
    int maxPathSum(TreeNode* root) {
        int ans = INT_MIN;
        getMax(root, ans);
        return ans;
    }
};
```

- 时间复杂度：使用的是先序遍历的递归模型，时间复杂度为O(N)；
- 空间复杂度：使用了常数个临时变量，故为O(1)；

**总结**

关于二叉树的题，大部分都是运用递归的思想解决。使用递归时，关键是通过分析当前节点与所求值的关系，理清递归时左右孩子需不需要返回，返回时需要返回什么样的值的问题，最后再看怎么处理空节点，从而得到递归的整个边界关系，完成递归代码的编写。
