# LeetCode 双指针


总结一下一些双指针题目的思路。

## 接雨水
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

### 思路
这道题与  ‘除自身以外数组的乘积’ 有异曲同工的之妙。在这里是找到 height[i] 中的左边界值和右边界值，然后用其中的最小值减去 height[i] 即可。而在  ‘除自身以外数组的乘积’ 中则是计算从 nums[0] 乘到 nums[i - 1] 的乘积 left[i] ，计算出从 nums[n - 1] 乘到 nums[i + 1] 的乘积 right[i] ，最后用 left[i] * right[i] 既是答案。

#### 实现过程
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int len = height.size();
        if (!len)
            return 0;
        vector<int> left(len);
        vector<int> right(len);
        left[0] = height[0];
        right[len - 1] = height[len - 1];

	  // 计算 hight [i] 的左边界值
        for (int i = 1; i < len; i++)
            left[i] = max(left[i - 1], height[i]);

	  // 计算 height[i] 的右边界值
        for (int i = len - 2; i >= 0; i--)
            right[i] = max(right[i + 1], height[i]);

        int ans = 0;
        for (int i = 0; i < len; i++)
            ans += min(left[i], right[i]) - height[i];
        return ans;
    }
};
```

- 时间复杂度：遍历了三次 height ，故为O(N)；
- 空间复杂度：使用了两个大小为 N 的数组记录边界值，故为O(N)；


## 训练计划I
教练使用整数数组 actions 记录一系列核心肌群训练项目编号。为增强训练趣味性，需要将所有奇数编号训练项目调整至偶数编号训练项目之前。请将调整后的训练项目编号以 数组 形式返回。

### 思路
采用双指针，指针i从左往右找偶数，指针 j 从右往左找奇数，各自找到之后交换两指针指向的元素，以此类推，直到 i = j 时既交换完毕。

#### 实现过程
```cpp
class Solution {
public:
    vector<int> trainingPlan(vector<int>& actions) {
        int len = actions.size();
        if (len <= 1)
            return actions;
        int i = 0;
        int j = len - 1;
        while (i < j) {
            while (i < j && (actions[i] & 1) == 1)
                i++;
            while (i < j && (actions[j] & 1) == 0)
                j--;
            std::swap(actions[i], actions[j]);
        }
        return actions;
    }
};
```

- 时间复杂度：总的来说，遍历了一次数组，故为O(N)；
- 孔家复杂度：O(1);
