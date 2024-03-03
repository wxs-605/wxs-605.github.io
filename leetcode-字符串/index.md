# LeetCode 字符串


总结记录一下字符串题的思路及做法。

## 字符串相加
给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和并同样以字符串形式返回。

你不能使用任何內建的用于处理大整数的库（比如 BigInteger）， 也不能直接将输入的字符串转换为整数形式。

### 思路
手动模拟整数相加的过程，每次得到的一个位上的结果插入到 `ans` 的第0个位置即可。

#### 实现过程
```cpp
class Solution {
public:
    string addStrings(string num1, string num2) {
        string ans("");
        int i = num1.size() - 1;
        int j = num2.size() - 1;
        int carry = 0;
        while (i >= 0 || j >= 0) {
            int x = 0;
            int y = 0;
            if (i >= 0)
                x = num1[i--] - '0';
            if (j >= 0)
                y = num2[j--] - '0';
            ans.insert(ans.begin(), ((x + y + carry) % 10) + '0');
            carry = (x + y + carry) / 10;
        }
        if (carry)
            ans.insert(ans.begin(), carry + '0');
        return ans;
    }
};
```

- 时间复杂度：遍历了两个字符串，故为O(M+N);
- 空间复杂度：不计入ans的大小的话，为O(1)；


## 有效的括号字符串

给你一个只包含三种字符的字符串，支持的字符类型分别是 '('、')' 和 '*'。请你检验这个字符串是否为有效字符串，如果是有效字符串返回 true 。

有效字符串符合如下规则：

- 任何左括号 '(' 必须有相应的右括号 ')'。
- 任何右括号 ')' 必须有相应的左括号 '(' 。
- 左括号 '(' 必须在对应的右括号之前 ')'。
- '*' 可以被视为单个右括号 ')' ，或单个左括号 '(' ，或一个空字符串。
- 一个空字符串也被视为有效字符串。

### 思路
`*`相当于万能字符，我们分别从左到右和从右到左检查两次字符串是否合法：
1. 从左到右：统计左括号数和`*`数，遇到右括号时，优先用左括号去抵消，再用`*`去抵消，最后都没有的话直接返回false；
2. 从右到左：统计右括号数和`*`数，遇到左括号时，优先用右括号去抵消，再用`*`去抵消，最后都没有的话直接返回false；
3. 两次检查都无误的话返回true；

**为什么不能只从左到右检查一次？**
因为从左到右合法的字符串反过来不一定合法。如`"*("`，按以上规则从左到右检查是合法的，但不是正确的；

#### 代码实现
```cpp
class Solution {
public:
    bool checkValidString(string s) {
        int len = s.size();
        int leftConut = 0;
        int startCount = 0;
        for(auto c : s){
            if(c == '(')
                leftConut++;
            else if(c == '*')
                startCount++;
            else{
                if(leftConut > 0)
                    leftConut--;
                else if(startCount > 0)
                    startCount--;
                else
                    return false;
            }
        }
        int rightCount = 0;
        startCount = 0;
        for(int i = len - 1;i >= 0;i--){
            if(s[i] == ')')
                rightCount++;
            else if(s[i] == '*')
                startCount++;
            else{
                if(rightCount > 0)
                    rightCount--;
                else if(startCount > 0)
                    startCount--;
                else
                    return false;
            }
        }
        return true;
    }
};
```

- 时间复杂度：遍历了两次字符串，故为O(N)；
- 空间复杂度：O(1);


## 破解闯关密码
闯关游戏需要破解一组密码，闯关组给出的有关密码的线索是：
- 一个拥有密码所有元素的非负整数数组 password
- 密码是 password 中所有元素拼接后得到的最小的一个数
请编写一个程序返回这个密码。

### 思路
先将password中元素转成string类型，然后按照 规则 `x + y < y + x` 的规则排序即可。最简单的方式就是调用排序库函数。

### 实现过程
```cpp
class Solution {
public:
    string crackPassword(vector<int>& password) {
        vector<string> pd;
        for (auto num : password)
            pd.push_back(std::to_string(num));
        std::sort(pd.begin(), pd.end(),
                  [](string x, string y) { return x + y < y + x; });
        string ans;
        for (auto num : pd)
            ans += num;
        return ans;
    }
};
```

- 时间复杂度：调用库排序函数，为O(NlogN);
- 空间复杂度：排序时的递归深度为O(logN)，故为O(logN);
