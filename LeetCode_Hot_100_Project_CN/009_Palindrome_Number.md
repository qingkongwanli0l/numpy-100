# 9. 回文数 (Palindrome Number)

### 问题描述
给定一个整数 `x`，如果 `x` 是一个回文整数，则返回 `true`。当一个整数从后向前读与从前向后读相同时，它就是回文。例如，`121` 是回文，而 `123` 不是。

该问题通常包含一个后续问题：“你能否在不将整数转换为字符串的情况下解决它？”
[查看LeetCode问题描述](https://leetcode.cn/problems/palindrome-number/)

### 最优解法思路
要在不将整数转换为字符串（并避免可能因反转整个数字而产生的潜在溢出问题）的情况下判断一个整数是否是回文，最优方法是仅反转数字的后半部分。然后将这个反转的后半部分与原始数字的前半部分进行比较。
1.  **首先处理边界情况：** 负数不是回文。如果 `x < 0`，返回 `false`。如果 `x` 不是 `0` 但以 `0` 结尾（即 `x % 10 == 0`），则它不可能是回文。所以，如果 `x % 10 == 0 && x != 0`，返回 `false`。如果 `x` 是 `0`，则它是回文，返回 `true`。
2.  **反转后半部分：** 初始化 `revertedNumber = 0`。通过取 `x` 的最后一位数字，将其附加到 `revertedNumber`，然后从 `x` 中移除该最后一位数字，来迭代构建 `revertedNumber`。只要 `x` 大于 `revertedNumber`，此过程就继续。
3.  **比较两半：** 当循环终止时，`x` 将表示前半部分，`revertedNumber` 表示反转的后半部分。如果原始数字具有偶数个数字，则 `x` 和 `revertedNumber` 应该相等。如果为奇数，则 `revertedNumber` 将包含中间数字；`x` 应该等于 `revertedNumber / 10`。
4.  数字 `x` 是回文，如果 (`x == revertedNumber`) 或 (`x == revertedNumber / 10`)。

### 解题步骤详解
1.  如果 `x < 0`，返回 `false`。
2.  如果 `x != 0` 且 `x % 10 == 0`，返回 `false`。（处理像 10, 120 这样的数字）
3.  如果 `x == 0`，返回 `true`。（或者，主要逻辑也能正确处理 0）。
4.  初始化 `revertedNumber = 0`。
5.  循环，当 `x > revertedNumber` 时：
    a.  `revertedNumber = revertedNumber * 10 + (x % 10)`。
    b.  `x = x / 10`。
6.  循环之后，如果 `x == revertedNumber`（对于原始数字位数为偶数的情况）或 `x == revertedNumber / 10`（对于原始数字位数为奇数的情况），则该数字是回文。返回此比较的结果。

### Java 代码实现
```java
class Solution {
    // 方法：检查整数 x 是否是回文数
    public boolean isPalindrome(int x) {
        // 边界情况 1：负数不是回文数。
        // 例如：-121 不是回文数。
        if (x < 0) {
            return false;
        }

        // 边界情况 2：如果数字以 0 结尾，但它本身不是 0，
        // 则它不可能是回文数。
        // 例如：10, 120。要使这些成为回文数，它们必须以 0 开头。
        // 0 本身是回文数（稍后由 x == revertedNumber 或 x == revertedNumber / 10 处理，
        // 或者可以显式检查：if (x == 0) return true;）。
        if (x % 10 == 0 && x != 0) {
            return false;
        }

        int revertedNumber = 0; // 这将存储 x 的反转的后半部分

        // 循环反转数字的后半部分。
        // 我们继续循环，只要原始数字 `x`（正在减少）
        // 大于 `revertedNumber`。当 `x <= revertedNumber` 时，
        // 我们已经处理了一半或略多于一半的数字。
        while (x > revertedNumber) {
            int digit = x % 10;          // 获取 x 的最后一位数字
            revertedNumber = revertedNumber * 10 + digit; // 将其附加到 revertedNumber
            x /= 10;                     // 从 x 中移除最后一位数字
        }

        // 循环之后，比较前半部分（保留在 x 中）和反转的后半部分（revertedNumber）。
        // 情况 1：原始数字具有偶数个数字。
        // 在这种情况下，x 和 revertedNumber 应该相同。
        // 例如：x_original = 1221。循环结束时 x = 12, revertedNumber = 12。(x == revertedNumber)
        //
        // 情况 2：原始数字具有奇数个数字。
        // 在这种情况下，revertedNumber 将比 x 多一位数字（x_original 的中间数字）。
        // 我们可以通过将其除以 10 来从 revertedNumber 中忽略中间数字。
        // 例如：x_original = 12321。循环结束时 x = 12, revertedNumber = 123。(x == revertedNumber / 10)
        return x == revertedNumber || x == revertedNumber / 10;
    }
}
```

### 核心数据结构
无，仅使用整型变量。这导致 O(1) 的空间复杂度。

### 主要算法思想
数字操作：使用模运算符 (`%`) 提取数字，并通过乘以 10 并添加数字来构造一个新数字。比较两半：核心思想是将数字的前半部分与反转的后半部分进行比较，巧妙地处理奇数和偶数位数的情况。

### 相关问题
*   整数反转 (LeetCode #7)
*   验证回文串 (LeetCode #125)
*   最长回文子串 (LeetCode #5)
```
