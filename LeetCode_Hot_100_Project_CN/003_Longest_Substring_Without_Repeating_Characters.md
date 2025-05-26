# 3. 无重复字符的最长子串 (Longest Substring Without Repeating Characters)

### 问题描述
给定一个字符串 `s` ，请你找出其中不含有重复字符的 最长子串 的长度。
[查看LeetCode问题描述](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

### 最优解法思路
该问题可以使用滑动窗口技术结合记录字符最后出现位置的方法来高效解决。“窗口”是由一个 `start` 和一个 `end` 指针定义的子串。当我们用 `end` 指针遍历字符串时，我们扩展这个窗口。为确保窗口只包含唯一字符，我们使用一个辅助数据结构（如 ASCII 字符的整数数组或哈希映射）来存储每个字符最近一次遇到的索引。当考虑字符 `s.charAt(end)` 时： 1. 如果此字符之前已经出现过 *并且* 其最后出现的索引位于我们当前窗口内（即 `lastSeenIndex[s.charAt(end)] >= start`），则意味着包含 `s.charAt(end)` 会在当前窗口中产生重复。为了解决这个问题，我们必须将窗口的 `start` 滑动到该字符前一次出现位置的紧邻后方：`start = lastSeenIndex[s.charAt(end)] + 1`。 2. 如果该字符之前未出现过，或者其最后一次出现的位置在当前 `start` 指针之前，则当前窗口 `[start, end]` 仍然是包含唯一字符的有效窗口。 3. 无论哪种情况，我们都将 `s.charAt(end)` 的最后出现索引更新为当前的 `end` 索引。 4. 当前有效的无重复字符子串的长度是 `end - start + 1`。我们不断更新我们找到的总体最大长度。这种方法确保每个字符最多被访问两次（一次由 `end` 指针访问，一次可能由 `start` 指针调整访问），从而实现 O(n) 的时间复杂度。空间复杂度为 O(min(m, n))，其中 n 是字符串的长度，m 是字符集的大小（例如，ASCII 为 128，小写英文字母为 26），这是由于 `lastSeenIndices` 存储造成的。如果字符集大小被认为是常数（如 ASCII 128），则空间复杂度为 O(1)。

### 解题步骤详解
1.  获取输入字符串 `s` 的长度。如果长度为 `0`，则返回 `0`。
2.  初始化 `maxLength = 0`。这将存储找到的最大长度。
3.  初始化 `start = 0`。此指针标记当前无重复字符子串窗口的开始。
4.  创建一个大小为 128 的整数数组 `lastSeenIndices`（假设为 ASCII 字符集）。将其所有元素初始化为 `-1`，表示尚未看到任何字符。（或者，可以使用 `HashMap<Character, Integer>`）。
5.  使用 `end` 指针从 `0` 到 `s.length() - 1` 遍历字符串 `s`。
    a.  令 `currentChar` 为 `s.charAt(end)`。
    b.  从 `lastSeenIndices[currentChar]` 中检查 `currentChar` 的最后记录索引。
    c.  如果 `lastSeenIndices[currentChar]` 大于或等于 `start`，则表示 `currentChar` 先前在当前活动窗口 `[start ... end-1]` 内被找到。为保持子串不重复，窗口的 `start` 必须移动到前一次出现位置的右侧。因此，更新 `start = lastSeenIndices[currentChar] + 1`。
    d.  将 `currentChar` 的最后出现索引更新为当前的 `end` 指针值：`lastSeenIndices[currentChar] = end`。
    e.  计算当前有效窗口的长度：`currentLength = end - start + 1`。
    f.  更新 `maxLength = Math.max(maxLength, currentLength)`。
6.  循环完成后，`maxLength` 将保存不含重复字符的最长子串的长度。返回 `maxLength`。

### Java 代码实现
```java
import java.util.Arrays; // 如果使用 Arrays.fill 则需要导入

class Solution {
    // 方法：找出不含有重复字符的最长子串的长度
    public int lengthOfLongestSubstring(String s) {
        // 获取字符串的长度
        int n = s.length();
        // 如果字符串为空，则最长子串的长度为 0
        if (n == 0) {
            return 0;
        }

        // 用于存储目前找到的最大长度的变量
        int maxLength = 0;
        // 数组，用于存储每个字符最后一次出现的索引（假设为 ASCII 字符集）
        // 数组的索引对应于字符的 ASCII 值。
        int[] lastSeenIndices = new int[128]; // 对于扩展 ASCII，使用 256。对于通用 Unicode，HashMap 更合适。
        // 将所有最后出现索引初始化为 -1，表示尚未看到任何字符。
        Arrays.fill(lastSeenIndices, -1);

        // 'start' 是当前无重复字符子串窗口的起始索引
        int start = 0;

        // 'end' 是当前无重复字符子串窗口的结束索引
        // 使用 'end' 指针遍历字符串
        for (int end = 0; end < n; end++) {
            // 获取当前 'end' 位置的字符
            char currentChar = s.charAt(end);

            // 检查当前字符是否之前出现过（其 lastSeenIndex 不是 -1）
            // 并且其最后出现的位置是否在当前窗口内 (>= start)。
            // 如果为 true，则表示此字符在当前窗口内是重复的。
            if (lastSeenIndices[currentChar] >= start) {
                // 要从窗口中移除重复字符，
                // 将窗口的 'start' 移动到其前一次出现位置的右侧。
                start = lastSeenIndices[currentChar] + 1;
            }

            // 将当前字符的最后出现索引更新为其当前位置 'end'。
            lastSeenIndices[currentChar] = end;

            // 计算当前有效窗口（无重复字符的子串）的长度。
            // 长度为 (end - start + 1)。
            int currentLength = end - start + 1;

            // 如果当前窗口的长度更大，则更新 maxLength。
            maxLength = Math.max(maxLength, currentLength);
        }

        // 返回找到的最大长度。
        return maxLength;
    }
}
```

### 核心数据结构
整数数组（作为频率/最后出现位置映射）：一个数组（例如，对于ASCII字符集是 `int[128]`）用于存储每个字符最后一次出现的索引。字符的ASCII值作为该数组的索引。这为更新和检索最后出现索引提供了O(1)的时间复杂度。也可以使用 `HashMap<Character, Integer>`，它为更大的字符集（如Unicode）提供了更大的灵活性，但操作的常数因子略高。

### 主要算法思想
滑动窗口：这是核心技术。窗口 `[start, end]` 代表当前正在评估的子字符串。它通过移动 `end` 来扩展，当在窗口内找到重复字符时通过移动 `start` 来收缩。贪心方法：在每一步中，我们都尽可能地扩展当前有效的子字符串。

### 相关问题
*   至多包含两个不同字符的最长子串 (LeetCode #159)
*   至多包含 K 个不同字符的最长子串 (LeetCode #340)
*   最小覆盖子串 (LeetCode #76)
*   串联所有单词的子串 (LeetCode #30)
*   水果成篮 (LeetCode #904)
```
