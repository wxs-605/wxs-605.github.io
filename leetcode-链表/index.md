# LeetCode 链表


今天刷了挺多链表的题，稍微总结一下思路。大部分的链表题，按照常规思路去想就可以解决，很少有特别精妙的方法。

## 两数相加
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。请你将两个数相加，并以相同形式返回一个表示和的链表。

### 思路
easy，就是用链表表示两个加数，然后将其相加。从两个链表的头部开始一个一个节点相机即可，注意一下进位就好。

- 时间复杂度： 遍历两个链表，故为O(N)；
- 空间复杂度： 新开了一个链表存储结果，所以空间复杂度为o(N);



## K个一组翻转链表
给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。k是一个正整数，它的值小于或等于链表的长度。如果节点总数不是k的整数倍，那么请将最后剩余的节点保持原有顺序。能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

### 思路
使用快慢指针，从原链表上一组一组的截取一段节点，然后将每一组节点反转、拼接起来即可。

- 时间复杂度：截取原链表，故需要遍历一次，在翻转的过程中遍历每一段链表，所以总的来看遍历了两次链表，故时间复杂度为O(N)；
- 空间复杂度： 使用了常数个临时变量，所以空间复杂度为O(1)；


## 随机链表的复制
给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。构造这个链表的 深拷贝。 深拷贝应该正好由 n 个 全新 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 next 指针和 random 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。复制链表中的指针都不应指向原链表中的节点 。

### 思路
这题较难的地方是对random指针的处理，而且题中没说节点的值是唯一的，这就有可能多个节点的值都一样，造成对random的处理变得复杂。但是，同样可以运用快慢指针来解决这个问题。先遍历一遍整个链表，复制每一个节点，不管random指针先。第二次遍历时，new_list和old_list同时从头节点开始遍历，如果old_list中random为空，则new_list中random也为空；如果random不为空时，让慢指针slow和快指针fast同时指向new_list的头节点，接着从old_list中random指向的节点开始向下移动，每移动一个节点，fast也移动一个节点。当old_list移动到末尾时，slow再和fast同时一步一步的移动，当fast也走到末尾时，此时slow指向的节点刚好是对应于random指向的节点。

- 时间复杂度： 最坏情况为o(N<sup>2</sup>);
- 空间复杂度：O(1)；


## 合并K个升序链表
给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

### 思路
easy，就是对链表数组做K路归并排序嘛一个while循环里嵌套个for循环就可以解决。

- 时间复杂度： 遍历了每一个节点，故为O(KN)；
- 空间复杂度： 使用了常数个临时变量，故空间复杂度为O(1)；


## LRU缓存
请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。实现 LRUCache 类：
- LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1
- void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

### 思路
使用哈希表加双向链表解决。哈希表可以在O(1)时间内查找到key对应的节点。这些哈希表上的节点用双向链表表示，把这些节点用一个头节点head串成一个循环链表。head->next指向的是第一个节点，head->pre指向的是尾节点。当访问一个节点或新建立一个节点时，就把这个节点放到head的后面，当容量超过capacity时，就删除掉尾节点。

#### 代码实现
```c++
class Node {
public:
    int key;
    int value;
    Node* pre;
    Node* next;
    Node(int k, int v) : key(k), value(v) { pre = next = nullptr; }
};

class LRUCache {
    int capacity;
    unordered_map<int, Node*> map;
    Node* head;

public:
    LRUCache(int capacity) {
        this->capacity = capacity;
        head = new Node(0, 0);
        head->next = head;
        head->pre = head;
    }

    int get(int key) {
        auto cur = map.find(key);
        if (cur == map.end())
            return -1;

	  // 将访问过的节点调到头节点后面；
        cur->second->pre->next = cur->second->next;
        cur->second->next->pre = cur->second->pre;
        cur->second->next = head->next;
        head->next->pre = cur->second;
        head->next = cur->second;
        cur->second->pre = head;
        return cur->second->value;
    }

    void put(int key, int value) {
        auto cur = map.find(key);
        if (cur != map.end()) {

		// 节点存在时，修改器value值， 并将其调到头节点后面；
            cur->second->value = value;
            cur->second->pre->next = cur->second->next;
            cur->second->next->pre = cur->second->pre;
            cur->second->next = head->next;
            head->next->pre = cur->second;
            head->next = cur->second;
            cur->second->pre = head;
            return;
        }

	  // 节点不存在，则新间节点，加入到头节点后面；
        Node* node = new Node(key, value);
        map[key] = node;
        node->next = head->next;
        head->next->pre = node;
        head->next = node;
        node->pre = head;
	
	  // 判断节点数量有没有达到阈值；
        if (map.size() <= capacity)
            return;
	  
	  // 到达阈值时，删除链表最后一个节点；
        Node* temp = head->pre;
        map.erase(temp->key);
        temp->pre->next = temp->next;
        temp->next->pre = temp->pre;
        delete temp;
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```
