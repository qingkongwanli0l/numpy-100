# 17. 电话号码的字母组合 (Letter Combinations of a Phone Number)

### 问题描述
给定一个包含数字 `2-9`（含）的字符串，返回该数字可能表示的所有可能的字母组合。答案可以按任何顺序返回。

数字到字母的映射（类似于电话按钮）如下：
*   '2': "abc"
*   '3': "def"
*   '4': "ghi"
*   '5': "jkl"
*   '6': "mno"
*   '7': "pqrs"
*   '8': "tuv"
*   '9': "wxyz"
注意 '1' 不映射到任何字母。

**示例：**
输入: `digits = "23"`
输出: `["ad","ae","af","bd","be","bf","cd","ce","cf"]`
[查看LeetCode问题描述](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

### 最优解法思路
此问题要求生成与电话键盘上给定数字序列对应的所有可能的字母组合。这是回溯算法的经典场景。核心思想是逐个字符地构建组合。对于输入字符串中的每个数字，我们迭代其所有可能的字母映射。我们将选定的字母附加到当前组合中，然后递归调用我们的函数来处理下一个数字。一旦我们处理完所有数字（即，我们当前的组合与输入数字字符串具有相同的长度），我们就形成了一个完整的组合并将其添加到我们的结果列表中。在递归调用返回后，我们通过移除最后添加的字母进行“回溯”。这使得循环可以继续并尝试当前数字的下一个可能字母，从而有效地探索决策树的所有分支。

### 解题步骤详解
1.  如果 `digits` 为空，则返回一个空列表。
2.  为 `digitToLetters` 创建一个字符串数组或映射（例如，`digitToLetters[2] = "abc"`）。
3.  初始化一个空的 `List<String> combinationsResult`。
4.  定义一个回溯函数 `backtrack(currentIndex, currentStringPath)`：
    a.  如果 `currentIndex == digits.length()`：将 `currentStringPath.toString()` 添加到 `combinationsResult`。返回。
    b.  获取 `lettersForCurrentDigit = digitToLetters[digits.charAt(currentIndex)]`（如果使用数组，则调整数组索引）。
    c.  对于 `lettersForCurrentDigit` 中的每个 `char letter`：
        i.  将 `letter` 附加到 `currentStringPath`。
        ii. 调用 `backtrack(currentIndex + 1, currentStringPath)`。
        iii.从 `currentStringPath` 中移除最后一个字符（回溯）。
5.  初始调用：`backtrack(0, new StringBuilder())`。
6.  返回 `combinationsResult`。

### Java 代码实现
```java
import java.util.ArrayList; // ArrayList 所需的导入
import java.util.List;     // List 接口所需的导入
// 如果使用数组进行映射，则不需要 Map，但 Map 也是一种常见的选择。

class Solution {
    // 用于存储最终组合的列表
    private List<String> combinations;
    // 数字到字母的映射。根据问题，索引 0 和 1 为空。
    private final String[] digitToLetters = {
        "",     // 0
        "",     // 1
        "abc",  // 2
        "def",  // 3
        "ghi",  // 4
        "jkl",  // 5
        "mno",  // 6
        "pqrs", // 7
        "tuv",  // 8
        "wxyz"  // 9
    };

    // 公共主方法
    public List<String> letterCombinations(String digits) {
        combinations = new ArrayList<>(); // 初始化结果列表
        // 如果输入数字字符串为 null 或为空，则返回空列表
        if (digits == null || digits.length() == 0) {
            return combinations;
        }
        // 开始回溯过程
        backtrack(0, new StringBuilder(), digits);
        return combinations;
    }

    // 递归回溯辅助函数
    private void backtrack(int index, StringBuilder currentPath, String digits) {
        // 基本情况：如果当前路径的长度等于输入数字的长度，
        // 则我们已经形成了一个完整的组合。
        if (index == digits.length()) {
            combinations.add(currentPath.toString()); // 将完整的组合添加到结果中
            return; // 结束此递归路径
        }

        // 获取要处理的当前数字字符
        char currentDigitChar = digits.charAt(index);
        // 将数字字符转换为整数，以用作 digitToLetters 映射的索引
        // (例如, '2' - '0' = 2)
        String lettersForDigit = digitToLetters[currentDigitChar - '0'];

        // 迭代当前数字的所有可能字母
        for (char letter : lettersForDigit.toCharArray()) {
            // 1. 选择：将当前字母附加到路径
            currentPath.append(letter);
            // 2. 探索：为下一个数字递归调用
            backtrack(index + 1, currentPath, digits);
            // 3. 取消选择（回溯）：移除最后一个字母以探索其他可能性
            currentPath.deleteCharAt(currentPath.length() - 1);
        }
    }
}
```

### 核心数据结构
**`List<String>`：** 用于存储生成的字母组合的最终列表。 **`StringBuilder`：** 用于在递归过程中高效地构建 `currentPath`（当前组合）。 **`String[]` (或 `Map<Character, String>`)：** 用于存储从数字（'2'-'9'）到其相应字母字符串的静态映射。

### 主要算法思想
**回溯：** 这是使用的基本算法。它通过为每个数字尝试字符然后撤销选择（回溯）以尝试其他字符来系统地探索所有可能的组合。这是概念决策树上的一种深度优先搜索 (DFS) 形式。

### 相关问题
*   生成括号 (LeetCode #22)
*   子集 (LeetCode #78)
*   全排列 (LeetCode #46)
*   组合总和 (LeetCode #39)
*   单词搜索 (LeetCode #79)
```
