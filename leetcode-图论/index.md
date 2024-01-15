# LeetCode 图论


小结一下今天LeetCode上关于图论的几道题。

## 岛屿数量
给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

### 思路
本质就是考察图的深度优先遍历，遍历图中的每个点，如果是'1'，则对其进行dfs，最后统计外层循环里调用了几次dfs，即可得知图中有几个岛屿。

- 时间复杂度：遍历完整个图，故为O(MN)；
- 空间复杂度：dfs采用递归实现，最坏情况下整个图为陆地，故为O(MN)；


## 腐烂的橘子
在给定的 m x n 网格 grid 中，每个单元格可以有以下三个值之一：
- 值 0 代表空单元格；
- 值 1 代表新鲜橘子；
- 值 2 代表腐烂的橘子；
每分钟，腐烂的橘子 周围 4 个方向上相邻 的新鲜橘子都会腐烂。返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1 。

### 思路
1. 先统计一下新鲜水果和坏水果的数量，如果没有新鲜水果和坏水果，则可直接返回0；

2. 令count = 3；则第一次腐烂的水果为 count - 1 = 2；将在第一分钟内被感染成腐烂的水果的值置为 count ，然后 count++ ，那么第二分钟内腐烂的水果为count - 1 = 3。此时不用再处理值为2的腐烂水果，因为其已经被第一感染腐烂的水果包围了，不能再感染新鲜水果。再将第二分钟感染成腐烂的水果的值置为 count ，然后 count++。以此类推，直到没有新鲜水果被感染时，即可返回结果；

3. 最终返回时，若没有未被感染的新鲜水果，则返回 count - 4 ，既是所需的最小时间；若有未被感染的新鲜水果，就返回 -1.


- 时间复杂度：设所需的最小时间为t，则上述过程中遍历了 t + 1 次网格，最坏情况下 t = M，故为O(M<sup>2</sup>N)；
- 空间复杂度：使用了常数个临时变量，故为O(1)；


## 课程表
你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。在选修某些课程之前需要一些先修课程。 先修课程按数组 prerequisites 给出，其中 prerequisites[i] = [ai, bi] ，表示如果要学习课程 ai 则 必须 先学习课程  bi 。

- 例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。

请你判断是否可能完成所有课程的学习？如果可以，返回 true ；否则，返回 false 。

### 思路一
构造一个 numCourses * numCourses 的bool类型图 matrix ，若修读课程 j ，时需先修读课程 i ，那么 matrix[i][j] = true ，表示从 i 指向 j ，i 是 j 的先驱节点。遍历每一个课程对，将 matrix 中对应的值设为 true 。若 i、j、k 中存在这样的关系：i <- j <- k ，那么将 matrix[k][j] 设为 true 之后， 也需要将 matrix[k][i] 设为 true 。最后，遍历 matrix ，若存在 matrix[i][j] == matrix[j][i] ，则说明这两个点成环，可直接返回 false；遍历完之后，未找到上述的情况，返回 true 。

- 时间复杂度：设 M 为 prerequisites 的长度，matrix 为 N * N 的矩阵，最坏情况下为O(N<sup>2</sup>)；
- 空间复杂度：建立了 matrix 作为邻接矩阵，故为O(N<sup>2</sup>)；


### 思路二
换种角度思考，若课程可以修读完成，则图中没有环，则可说明图中存在拓扑序列。所以可以把课程对当前边，对这个图进行拓扑排序，若得到的拓扑序列中节点的数量等于 numCourses ，则说明图中没有环，课程可以正常修读完成，否则说明图中有环，课程不可以正常修读完成。

#### 拓扑序列的模板代码
```
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
	  // matrix 相当于邻接表，存储图中的边；
        vector<int> matrix[numCourses];

	  // in_degree 记录每个顶点的入度；
        vector<int> in_degree(numCourses, 0);

	  // 计算各边的入度；
        for (auto& e : prerequisites) {
            matrix[e[1]].push_back(e[0]);
            in_degree[e[0]]++;
        }

	  // 辅助队列q，用以实现拓扑排序，用栈也可；
        queue<int> q;

	  // 找到第一个入度为零的顶点，并加入到队列中；
        for (int i =0;i<numCourses;i++) {
            if (!in_degree[i])
                q.push(i);
        }

	  // 进行拓扑排序， 用 count 统计序列中顶点的数量；
        int count = 0;
        while (!q.empty()) {
            int idx = q.front();
            q.pop();
            count++;
            for (auto& i : matrix[idx]) {

		    // 若入度减一之后为零，将其加入到队列中；
                if (--in_degree[i] == 0)
                    q.push(i);
            }
        }
        return count == numCourses;
    }
};
```
这段代码为拓扑排序的模板的代码，贴这供以后学习用~~


- 时间复杂度：对图中的边均进行了两次遍历，同时访问了一次入度数组，故为O(M + N)；
- 空间复杂度：存储了图中的边及入读，故为O(M + N);


## 实现Trie(前缀数)
Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。请你实现Trie()类：
- Trie() 初始化前缀树对象；
- void insert(String word) 向前缀树中插入字符串 word ；
- boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false ；
- boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false ；


### 思路
首先什么是 **前缀树** ？

前缀数，顾名思义就是前缀形成树，也称为字典树，是一种用于高效存储和检索字符串的数据结构。它的主要特点是将输入的字符串拆分成字符序列，并通过字符之间的关联关系来组织和表示这些字符串。例如在百度中输入C++，会出现一大堆联想词语，都是以C++作为前缀的词语。前缀树的基本思想是利用共同的前缀来减少存储空间和搜索时间。它以树状结构存储字符序列，从根节点开始，每个节点代表一个字符，节点之间通过指针连接形成树的结构。从根节点到每个叶子节点的路径都代表一个完整的字符串。（*注意*根节点不存储字符）

前缀数的**结构**？

在前缀树中，一个节点一般包含两个部分：
- 字符结尾标志，表示当前字符是否是字符串的结尾；
- 子节点指针：指向当前节点的子节点，通常使用数组或哈希表来存储多个子节点，其中数组或哈希表的大小取决于字符集的大小。

前缀数**操作**的实现？

1. 插入操作：将一个字符串插入前缀树的过程是逐个字符进行的。从根节点开始，根据每个字符找到对应的子节点（如果不存在就创建一个），然后继续处理下一个字符，直到插入完整个字符串。

2. 搜索操作：在前缀树中搜索一个字符串时，也是逐个字符进行的。从根节点开始，根据每个字符找到对应的子节点，如果遇到空指针或者没有对应的子节点，则表示该字符串不存在于前缀树中。

3. 前缀匹配操作：前缀树最大的优势之一是可以快速找到具有特定前缀的所有字符串。从根节点开始，根据每个字符找到对应的子节点，直到达到前缀的最后一个字符。然后，可以通过遍历子树或使用深度优先搜索等方法，获取所有以该前缀开头的字符串。

4. 删除操作：删除一个字符串时，需要从前缀树中将其对应的节点递归地删除。如果删除节点后导致其父节点成为叶子节点且没有其他子节点，则可以进一步删除该父节点，以此类推，直到遇到一个非叶子节点或者有其他子节点的节点。

#### 实现代码(不包含删除操作)
```
class Trie {
    bool isEnd;
    Trie* next[26];

public:
    Trie() {
        isEnd = false;
        for(auto & e : next)
            e = nullptr;
    }

    void insert(string word) {
        Trie* cur_node = this;
        for (auto ch : word) {
            if (cur_node->next[ch - 'a'] == nullptr) {
                cur_node->next[ch - 'a'] = new Trie();
            }
            cur_node = cur_node->next[ch - 'a'];
        }
        cur_node->isEnd = true;
    }

    bool search(string word) {
        Trie* cur_node = this;
        for (auto ch : word) {
            cur_node = cur_node->next[ch - 'a'];
            if (cur_node == nullptr)
                return false;
        }
        return cur_node->isEnd;
    }

    bool startsWith(string prefix) { 
        Trie* cur_node = this;
        for (auto ch : prefix) {
            cur_node = cur_node->next[ch - 'a'];
            if (cur_node == nullptr)
                return false;
        }
        return true;
    }
};
```

