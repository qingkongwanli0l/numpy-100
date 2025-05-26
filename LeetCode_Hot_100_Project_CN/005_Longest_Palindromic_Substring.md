# 5. 最长回文子串 (Longest Palindromic Substring)

### 问题描述
给定一个字符串 `s` ，请你找出其中可以找到的 最长回文子串 。
[查看LeetCode问题描述](https://leetcode.cn/problems/longest-palindromic-substring/)

### 最优解法思路
“中心扩展”策略是查找最长回文子串的一种有效方法。核心思想是遍历字符串中的每个字符，并将其视为回文的潜在中心。由于回文可以具有奇数长度（例如，“aba”，以 'b' 为中心）或偶数长度（例如，“abba”，以两个 'b' 之间的间隙为中心），我们需要为每个可能的中心检查这两种情况。对于字符串中的每个索引 `i`：1. 奇数长度回文：我们将 `s[i]` 视为单个字符中心。然后我们尝试从这个中心向外扩展，一个指针向左移动 (`left = i`)，另一个向右移动 (`right = i`)。只要 `left` 在字符串边界内，`right` 在字符串边界内，并且 `s.charAt(left)` 等于 `s.charAt(right)`，我们就有一个回文。我们继续扩展。2. 偶数长度回文：我们将 `s[i]` 和 `s[i+1]` 之间的间隙视为中心。我们初始化 `left = i` 和 `right = i+1`。只要条件（`left >= 0`，`right < s.length()`，`s.charAt(left) == s.charAt(right)`）成立，我们就向外扩展。对于每次扩展（奇数和偶数），我们计算找到的回文的长度。我们跟踪到目前为止遇到的最长回文的起始索引和最大长度。检查完所有可能的中心后，与记录的起始索引和最大长度对应的子串将是我们的答案。这种方法的时间复杂度为 O(n^2)，因为有 2n-1 个潜在中心，并且在最坏情况下每次扩展最多可能需要 O(n) 时间。空间复杂度为 O(1)。

### 解题步骤详解
1.  处理边界情况：如果输入字符串 `s` 为 null 或其长度小于 1，则返回一个空字符串。如果其长度为 1，则字符串本身就是最长的回文。
2.  初始化 `currentStart = 0` 和 `currentMaxLength = 1` 以存储找到的最长回文子串的起始索引和长度。这些被初始化为表示字符串的第一个字符作为长度为 1 的默认回文。
3.  使用索引 `i` 从 `0` 到 `s.length() - 1` 遍历字符串 `s`。对于每个 `i`：
    a.  **检查奇数长度回文：** 调用一个辅助函数，例如 `expandAroundCenter(s, i, i)`。此函数将从中心 `i`（`left` 和 `right` 指针都从 `i` 开始）扩展。
    b.  **检查偶数长度回文：** 调用相同的辅助函数 `expandAroundCenter(s, i, i + 1)`。此函数将从 `i` 和 `i+1` 之间的中心（`left` 指针从 `i` 开始，`right` 指针从 `i+1` 开始）扩展。
4.  `expandAroundCenter(s, left, right)` 辅助函数的工作方式如下：
    a.  当 `left` 指针非负，`right` 指针在字符串长度内，并且字符 `s.charAt(left)` 和 `s.charAt(right)` 相等时：
        i.  递减 `left`（向左移动）。
        ii. 递增 `right`（向右移动）。
    b.  循环终止后，实际的回文是从 `left + 1` 到 `right - 1`。
    c.  计算找到的这个回文的长度：`length = (right - 1) - (left + 1) + 1 = right - left - 1`。
    d.  将此 `length` 与 `currentMaxLength` 进行比较。如果 `length > currentMaxLength`：
        i.  更新 `currentMaxLength = length`。
        ii. 更新 `currentStart = left + 1`。
5.  遍历完所有可能的中心（所有 `i`）后，值 `currentStart` 和 `currentMaxLength` 将定义最长的回文子串。
6.  返回 `s.substring(currentStart, currentStart + currentMaxLength)`。

### Java 代码实现
```java
class Solution {
    // 这些字段将存储找到的最长回文的起始索引和长度。
    // 使用成员变量可以简化从辅助方法进行的更新。
    private int currentStart = 0;
    private int currentMaxLength = 0;

    // 查找最长回文子串的主方法
    public String longestPalindrome(String s) {
        // 处理边界情况：null 字符串、空字符串或单字符字符串
        if (s == null || s.length() < 1) {
            return ""; // 如果输入为 null 或空，则返回空字符串
        }
        if (s.length() == 1) {
            return s; // 单字符字符串是长度为 1 的回文
        }

        // 初始化，以第一个字符作为长度为 1 的默认回文
        // 这确保了如果没有找到更长的回文，至少会返回单个字符。
        currentStart = 0;
        currentMaxLength = 1;

        // 遍历字符串的每个字符，将其视为潜在的中心
        for (int i = 0; i < s.length(); i++) {
            // 情况 1：奇数长度回文
            // 中心是字符 s[i] 本身。
            expandAroundCenter(s, i, i);

            // 情况 2：偶数长度回文
            // 中心在 s[i] 和 s[i+1] 之间。
            // 此检查是安全的，因为 expandAroundCenter 会处理 'right' 的边界条件。
            expandAroundCenter(s, i, i + 1);
        }

        // 提取并返回找到的最长回文子串
        return s.substring(currentStart, currentStart + currentMaxLength);
    }

    // 围绕给定中心（或多个中心）扩展的辅助方法
    // left 和 right 是中心的初始指针。
    // 对于奇数长度：left = right = center_index
    // 对于偶数长度：left = center_index, right = center_index + 1
    private void expandAroundCenter(String s, int left, int right) {
        // 只要满足以下条件就向外扩展：
        // 1. 'left' 指针在字符串边界内 (>= 0)
        // 2. 'right' 指针在字符串边界内 (< s.length())
        // 3. 'left' 和 'right' 位置的字符相同
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;  // 将 left 指针向左移动
            right++; // 将 right 指针向右移动
        }

        // 循环之后，实际的回文在 (left + 1) 和 (right - 1) 之间。
        // 例如，如果 s="aba", i=1:
        // 初始：left=1, right=1。s[1]==s[1] ('b'=='b')。循环：left=0, right=2。
        // s[0]==s[2] ('a'=='a')。循环：left=-1, right=3。
        // 循环终止。回文是从 left+1 = 0 到 right-1 = 2。（"aba"）
        // 长度 = (right - 1) - (left + 1) + 1 = right - left - 1。
        int currentPalindromeLength = right - left - 1;

        // 如果通过此扩展找到的回文比目前找到的任何回文都长
        if (currentPalindromeLength > currentMaxLength) {
            // 更新最大长度
            currentMaxLength = currentPalindromeLength;
            // 更新这个新的最长回文的起始索引
            // 起始索引是 (left + 1)
            currentStart = left + 1;
        }
    }
}
```

### 核心数据结构
无，除了输入字符串和用于跟踪最长回文的起始和长度的变量之外。这使得辅助空间复杂度为 O(1)。

### 主要算法思想
中心扩展：这是核心算法模式。它涉及检查回文的每个可能中心并向外扩展。双指针：`left` 和 `right` 指针用于扩展过程。

### 相关问题
*   回文子串 (LeetCode #647)
*   最长回文子序列 (LeetCode #516)
*   最短回文串 (LeetCode #214)
*   验证回文串 (LeetCode #125)
```
