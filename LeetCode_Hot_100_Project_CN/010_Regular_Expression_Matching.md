# 10. 正则表达式匹配 (Regular Expression Matching)

### 问题描述
给定一个输入字符串 `s` (文本) 和一个模式 `p`，实现支持以下特殊字符的正则表达式匹配：
*   `.` (点)：匹配任何单个字符。
*   `*` (星号)：匹配零个或多个前面的元素。
匹配必须覆盖**整个**输入字符串 `s`，而不仅仅是其中的一部分。
[查看LeetCode问题描述](https://leetcode.cn/problems/regular-expression-matching/)

### 最优解法思路
此问题可以使用动态规划解决。我们定义 `dp[i][j]` 为一个布尔值，如果字符串 `s` 的前 `i` 个字符与模式 `p` 的前 `j` 个字符匹配，则为 `true`，否则为 `false`。我们的目标是找到 `dp[s.length()][p.length()]`。

**初始化：**
*   `dp[0][0] = true`：空文本字符串匹配空模式。
*   **第一行 (`dp[0][j]`)：** 如果 `p.charAt(j-1)` 是 `'*'`，这个 `*` 可以消除 `p.charAt(j-2)`。因此，`dp[0][j] = dp[0][j-2]` (要求 `j >= 2`)。
*   **第一列 (`dp[i][0]`)：** 对于 `i > 0` 为 `false`。

**转移 (填充 `dp[i][j]`)：
令 `sChar = s.charAt(i-1)`，`pChar = p.charAt(j-1)`。
1.  **如果 `pChar == '.'` 或 `pChar == sChar`：** `dp[i][j] = dp[i-1][j-1]`。
2.  **如果 `pChar == '*'`：** 令 `prev_p_char = p.charAt(j-2)` (要求 `j >= 2`)。
    `dp[i][j] = dp[i][j-2]` (匹配 `prev_p_char` 的零次出现)
    `||` ( `(sChar == prev_p_char || prev_p_char == '.')` `&& dp[i-1][j]` ) (匹配一次或多次出现)。
3.  **否则：** `dp[i][j] = false`。

### 解题步骤详解
1.  令 `m = s.length()` 且 `n = p.length()`。
2.  创建一个二维布尔数组 `dp[m+1][n+1]`。
3.  **初始化基本情况：**
    a.  `dp[0][0] = true`。
    b.  对于 `j` 从 1 到 `n`：如果 `p.charAt(j-1) == '*'` 且 `j >= 2`，则 `dp[0][j] = dp[0][j-2]`。
4.  **填充表格：** 迭代 `i` 从 1 到 `m`：迭代 `j` 从 1 到 `n`：
    a.  令 `sCurrentChar = s.charAt(i-1)` 且 `pCurrentChar = p.charAt(j-1)`。
    b.  如果 `pCurrentChar == sCurrentChar` 或 `pCurrentChar == '.'`：`dp[i][j] = dp[i-1][j-1]`。
    c.  否则如果 `pCurrentChar == '*'` (要求 `j >= 2` 以获取 `p.charAt(j-2)`)：
        i.  `boolean matchZero = dp[i][j-2]`。
        ii. `boolean matchOneOrMore = false;` 如果 `sCurrentChar == p.charAt(j-2)` 或 `p.charAt(j-2) == '.'`，则 `matchOneOrMore = dp[i-1][j]`。
        iii. `dp[i][j] = matchZero || matchOneOrMore`。
    d.  否则：`dp[i][j] = false` (默认)。
5.  返回 `dp[m][n]`。

### Java 代码实现
```java
class Solution {
    // 方法：检查字符串 s 是否匹配模式 p，支持 '.' 和 '*'
    public boolean isMatch(String s, String p) {
        int m = s.length(); // 文本字符串的长度
        int n = p.length(); // 模式字符串的长度

        // dp[i][j] 表示 s 的前 i 个字符是否匹配 p 的前 j 个字符
        boolean[][] dp = new boolean[m + 1][n + 1];

        // 基本情况 1：空字符串 s 和空模式 p 总是匹配
        dp[0][0] = true;

        // 基本情况 2：空字符串 s 和非空模式 p
        // 处理像 "a*", ".*", "x*y*z*" 这样匹配空字符串的模式
        // p.charAt(j-1) 对应 dp[0][j]
        for (int j = 1; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                // 如果当前模式字符是 '*'，它可以匹配前面元素的零次出现。
                // 因此，dp[0][j] (s="", p=p[0...j-1]) 取决于 dp[0][j-2] (s="", p=p[0...j-3])。
                // 这仅在 j >= 2 时有效（以便 p.charAt(j-2) 存在）。
                if (j >= 2) {
                    dp[0][j] = dp[0][j - 2];
                }
                // 如果 j=1 (模式只是 "*")，dp[0][1] 保持 false (无效模式或不匹配空字符串)。
            }
            // 否则 dp[0][j] 默认为 false (例如 s="", p="a")
        }
        // 基本情况 3：非空字符串 s 和空模式 p (对于 i > 0 的 dp[i][0])
        // 这些默认已经是 false，这是正确的。

        // 填充 dp 表的其余部分
        for (int i = 1; i <= m; i++) { // 遍历文本 s
            for (int j = 1; j <= n; j++) { // 遍历模式 p
                char sChar = s.charAt(i - 1); // s 中的当前字符
                char pChar = p.charAt(j - 1); // p 中的当前字符

                if (pChar == '.' || pChar == sChar) {
                    // 情况 1：当前模式字符是 '.' (匹配任何 sChar)
                    // 或者当前模式字符匹配当前 sChar。
                    // 匹配取决于前面的子字符串是否匹配。
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (pChar == '*') {
                    // 情况 2：当前模式字符是 '*'。
                    // '*' 作用于模式中它前面的字符，即 p.charAt(j-2)。
                    // 这要求 j >= 2。

                    // 子情况 2a：'*' 匹配 p.charAt(j-2) 的零次出现。
                    // 那么，如果 s[0...i-1] 匹配 p[0...j-3] (不含 p.charAt(j-2)* 的模式)，则 dp[i][j] 为 true。
                    // 这对应于 dp[i][j-2]。
                    if (j >= 2) {
                        dp[i][j] = dp[i][j - 2];
                    }

                    // 子情况 2b：'*' 匹配 p.charAt(j-2) 的一次或多次出现。
                    // 如果当前 sChar 匹配 p.charAt(j-2)
                    // (或 p.charAt(j-2) 是 '.')，则这是可能的。
                    // 如果它们匹配，那么如果 s[0...i-2] (不含 sChar 的 s)
                    // 匹配 p[0...j-1] (直到当前 '*' 的模式)，则 dp[i][j] 可能为 true。这是 dp[i-1][j]。
                    if (j >= 2 && (sChar == p.charAt(j - 2) || p.charAt(j - 2) == '.')) {
                        // 如果 dp[i][j] 已经为 true (因为匹配零次出现)，
                        // 它仍然为 true。否则，考虑这种新的可能性。
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                    // 如果 j=1 且 pChar 是 '*'，dp[i][1] 保持 false (根据初始化或无效模式)。
                }
                // 否则 (pChar 是一个与 sChar 不匹配的字面字符，并且不是 '*')
                // dp[i][j] 默认为 false。
            }
        }

        // 最终结果是整个字符串 s 是否匹配整个模式 p
        return dp[m][n];
    }
}
```

### 核心数据结构
**二维布尔数组 (`dp[][]`)：** 此表存储子问题的中间结果。如果 `s` 的前 `i` 个字符匹配 `p` 的前 `j` 个字符，则 `dp[i][j]` 为 true。维度是 `(s.length() + 1) x (p.length() + 1)`。

### 主要算法思想
**动态规划 (DP)：** 此问题表现出最优子结构和重叠子问题，使得 DP 成为标准方法。该解决方案建立在较小前缀匹配结果的基础上。

### 相关问题
*   通配符匹配 (LeetCode #44)
*   编辑距离 (LeetCode #72)
*   最长公共子序列 (LeetCode #1143)
```
