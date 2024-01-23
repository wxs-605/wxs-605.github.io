# LeetCode 二分查找


这两天刷了二分查找的题，感觉对二分的理解进了一步，这里做个小总结。

## 在排序数组中查找元素的第一个和最后一个位置
给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

### 思路
采用二分查找找到target，然后再找到边界即可。

- 时间复杂度：最坏情况下，数组中的元素均一致，故为O(N)，一般情况下为O(logN);
- 空间复杂度：采用了常数个临时变量，故为O(1)；


## 搜索旋转排序数组
整数数组 nums 按升序排列，数组中的值 互不相同 。

在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。

给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。

你必须设计一个时间复杂度为 O(log n) 的算法解决此问题。

### 思路
数组旋转后，以第一个元素为枢轴值，采用二分查找。如果 nums[mid] >= nums[0] , 说明当前遍历的是 nums 的前半部分，且[nums[0], nums[mid]]有序，若是target位于前半部分，则继续进行二分查找即可；若target不位于前半部分，则令 low = mid + 1, 往后半部分进行查找即可。如果 nums[mid] < nums[0]，说明当前便利的是后半部分，且[nums[mid], nums[len - 1]] 有序。按照上面的思路进行搜索即可。

#### 实现过程
```
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int low = 0;
        int high = nums.size() - 1;
        int mid = (low + high) / 2;
        int len = nums.size();
        while (low <= high) {
            mid = (low + high) / 2;
            if (nums[mid] == target)
                return mid;
            if (nums[mid] >= nums[0]) {
                if (nums[mid] > target && target >= nums[0])
                    high = mid - 1;
                else
                    low = mid + 1;
            } else {
                if (nums[mid] < target && target <= nums[len - 1])
                    low = mid + 1;
                else
                    high = mid - 1;
            }
        }
        return -1;
    }
};
```

- 时间复杂度：二分查找，故为O(logN);
- 空间复杂度：O(1)，使用常数个临时变量；

**注意**
只要有序，就可以二分查找，这个有序不一定非要整体有序，部分有序的话也可以对部分进行二分查找。


## 寻找旋转排序数组中的最小值
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：
- 若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
- 若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]
注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个元素值 互不相同 的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

你必须设计一个时间复杂度为 O(log n) 的算法解决此问题。

### 思路
思路与上一题大差不差，同样以第一个元素为枢轴进行比较，如果 nums[mid] > nums[0] ，说明当前位于前半部分，最小的元素在后半部分，需要后移，故令 low = mid + 1;否则说明遍历的是后半部分，令 high = mid - 1， 往前移找到最小值即可。

#### 实现过程
```
class Solution {
public:
    int findMin(vector<int>& nums) {
        int len = nums.size();
        if (len == 1 || nums[0] < nums[len - 1])
            return nums[0];
        int low = 0;
        int high = len - 1;
        int mid = (low + high) / 2;
        while (low <= high) {
            mid = (low + high) / 2;
            if (nums[mid] >= nums[0]) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return nums[low];
    }
};
```

- 时间复杂度：二分查找，故为O(logN);
- 空间复杂度：O(1)；
