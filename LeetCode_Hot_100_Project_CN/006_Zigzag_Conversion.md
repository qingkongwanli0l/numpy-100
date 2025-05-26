# 6. Z 字形变换 (Zigzag Conversion)

### 问题描述
字符串 "PAYPALISHIRING" 在给定行数上以 Z 字形模式书写，如下所示：（您可能希望以可视化方式显示此模式以便更好地理解）
```
P   A   H   N
A P L S I I G
Y   I   R
```
然后逐行读取："PAHNAPLSIIGYIR"。

编写代码，使其在给定字符串 `s` 和整数 `numRows` 的情况下，进行此转换。
[查看LeetCode问题描述](https://leetcode.cn/problems/zigzag-conversion/)

### 最优解法思路
该问题要求我们将一个字符串重新排列成跨越指定行数的 Z 字形图案，然后逐行读出。实现这一目标最直接的方法是模拟字符的放置。我们可以维护一个 `StringBuilder` 对象列表，其中每个 `StringBuilder` 对应 `numRows` 中的一行。然后我们逐个字符地遍历输入字符串。对于每个字符，我们确定它在 Z 字形图案中属于哪一行，并将其附加到相应的 `StringBuilder` 中。为了管理行分配，我们跟踪 `currentRow`（当前行）和 `currentDirection`（移动方向，向下或向上）。`currentRow` 从 0 开始。`currentDirection` 最初表示向下移动。当 `currentRow` 到达顶部（第 0 行）或底部（第 `numRows - 1` 行）时，`currentDirection` 会反转。处理完输入字符串中的所有字符后，这些 `StringBuilder` 将按正确顺序包含每行的字符。从第一行到最后一行连接这些 `StringBuilder` 即可得到最终的 Z 字形转换字符串。此方法的时间复杂度为 O(N)，其中 N 是字符串的长度（因为每个字符处理一次），空间复杂度为 O(N)（用于在 `StringBuilder` 中存储字符）。

### 解题步骤详解
1.  处理边界情况：如果 `numRows` 为 1，或者如果 `numRows` 大于或等于字符串 `s` 的长度，则 Z 字形图案不会改变字符串的顺序。在这些情况下，返回原始字符串 `s`。
2.  创建一个 `StringBuilder` 对象列表，名为 `rows`。此列表的大小应为 `numRows`。使用新的 `StringBuilder` 初始化此列表的每个元素。
3.  初始化一个整数 `currentRow = 0`。这将跟踪应向其附加字符的当前行。
4.  初始化一个布尔值 `goingDown = false`。此变量将确定跨行的移动方向。（它最初设置为 `false`，因为第一次移动将是到第 0 行，然后方向将翻转为 `true` 以向下移动，或者可以将其初始化为 `true` 并对第一个字符进行略微不同的处理）。
5.  遍历输入字符串 `s` 的每个字符 `c`：
    a.  将字符 `c` 附加到 `rows` 列表中索引为 `currentRow` 的 `StringBuilder` 中：`rows.get(currentRow).append(c);`。
    b.  确定是否需要更改方向：如果 `currentRow` 为 `0`（第一行）或 `currentRow` 为 `numRows - 1`（最后一行），则翻转 `goingDown` 的值（例如，`goingDown = !goingDown;`）。
    c.  根据 `goingDown` 方向更新 `currentRow`：如果 `goingDown` 为 `true`，则递增 `currentRow`。如果 `goingDown` 为 `false`，则递减 `currentRow`。
6.  循环处理完所有字符后，创建一个名为 `result` 的新 `StringBuilder`。
7.  遍历 `rows` 列表（从第 0 行的 `StringBuilder` 到第 `numRows - 1` 行的 `StringBuilder`）。将 `rows` 中每个 `StringBuilder` 的内容附加到 `result` `StringBuilder` 中。
8.  将 `result` `StringBuilder` 转换为字符串并返回。

### Java 代码实现
```java
import java.util.ArrayList; // ArrayList 所需的导入
import java.util.List;      // List 接口所需的导入

class Solution {
    // 方法：根据 numRows 将字符串转换为 Z 字形图案
    public String convert(String s, int numRows) {
        // 边界情况：如果 numRows 为 1，则 Z 字形图案就是字符串本身。
        // 此外，如果字符串长度小于或等于 numRows，则实际上没有进行 Z 字形转换。
        if (numRows == 1 || s.length() <= numRows) {
            return s; // 返回原始字符串
        }

        // 创建一个 StringBuilder 列表，其中每个 StringBuilder 代表一行
        List<StringBuilder> rows = new ArrayList<>();
        // 用一个空的 StringBuilder 初始化每一行
        for (int i = 0; i < numRows; i++) {
            rows.add(new StringBuilder());
        }

        // currentRow 跟踪 Z 字形图案中的当前行索引
        int currentRow = 0;
        // goingDown 指示遍历方向（true 表示向下，false 表示向上）
        boolean goingDown = false; // 开始时可能向上移动，以便为第 0 行翻转向下

        // 遍历输入字符串中的每个字符
        for (char c : s.toCharArray()) {
            // 将当前字符附加到 currentRow 的 StringBuilder 中
            rows.get(currentRow).append(c);

            // 检查是否需要更改方向：
            // - 如果我们在顶行 (currentRow == 0)
            // - 或者如果我们在底行 (currentRow == numRows - 1)
            if (currentRow == 0 || currentRow == numRows - 1) {
                goingDown = !goingDown; // 翻转方向
            }

            // 根据方向更新 currentRow
            // 如果 goingDown 为 true，则移动到下一行 (currentRow + 1)
            // 如果 goingDown 为 false，则移动到上一行 (currentRow - 1)
            currentRow += goingDown ? 1 : -1;
        }

        // 将 rows 列表中的所有 StringBuilder 连接起来形成最终结果
        StringBuilder result = new StringBuilder();
        for (StringBuilder rowContent : rows) {
            result.append(rowContent);
        }

        // 将最终的 StringBuilder 转换为 String 并返回
        return result.toString();
    }
}
```

### 核心数据结构
`List<StringBuilder>`：这是主要的数据结构。列表中的每个 `StringBuilder` 累积属于 Z 字形图案一行的字符。使用 `StringBuilder` 可以有效地附加字符。

### 主要算法思想
模拟/遍历：该算法模拟字符以 Z 字形方式书写时的路径。它跟踪当前行和方向以正确放置每个字符。

### 相关问题
*   螺旋矩阵 (LeetCode #54)
```
