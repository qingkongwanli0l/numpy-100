# 8. 字符串转换整数 (atoi) (String to Integer (atoi))

### 问题描述
实现 `myAtoi(String s)` 函数，使其能将字符串转换成一个 32 位有符号整数。该函数应模仿 C/C++ `atoi` 函数的行为。

转换算法如下：
1.  **忽略前导空白字符：** 读入并丢弃任何前导空白字符（只考虑空格 ' '）。
2.  **检查符号：** 在空白字符之后，检查下一个字符是 '-' 还是 '+'。如果存在，读取该字符以确定结果的符号。如果两者都不存在，则假定结果为正。
3.  **读取数字：** 只要字符是数字（'0'-'9'），就继续读入后续字符。当遇到非数字字符或到达字符串末尾时停止读取。
4.  **转换为整数：** 将读取到的数字序列转换为整数。如果没有读取到数字，则整数为 0。
5.  **处理上溢/下溢：** 如果结果整数超出了 32 位有符号整数范围 `[-2^31, 2^31 - 1]`，则对其进行截断。小于 `-2^31` 的整数应截断为 `-2^31`。大于 `2^31 - 1` 的整数应截断为 `2^31 - 1`。
6.  返回最终的整数。
[查看LeetCode问题描述](https://leetcode.cn/problems/string-to-integer-atoi/)

### 最优解法思路
该问题要求根据一组特定规则仔细解析输入字符串，并处理各种边界情况，包括空白、符号、非数字字符以及在 32 位有符号整数限制内的整数上溢/下溢。该方法包括遍历字符串，维护当前的解析状态： 1. 跳过空白：将索引前进到任何前导空格之后。 2. 确定符号：检查当前索引处的字符是 '+' 还是 '-'。记录符号（默认为正）并前进索引。 3. 数字转换和溢出处理：当字符是数字时进行迭代。在每个步骤中：将数字字符转换为其整数值。在将此数字附加到累积的 `result`（最初构建为正数）之前，检查潜在的溢出。如果 `result > Integer.MAX_VALUE / 10`，或者 (`result == Integer.MAX_VALUE / 10` 且 `current_digit > Integer.MAX_VALUE % 10`)，则添加当前数字将导致 `result * 10 + current_digit` 超过 `Integer.MAX_VALUE`。在这种情况下，如果符号为正，则截断为 `Integer.MAX_VALUE`；如果为负，则截断为 `Integer.MIN_VALUE`。如果没有即将发生的溢出，则更新 `result = result * 10 + current_digit`。 4. 最终结果：将确定的符号应用于累积的 `result`。截断机制应确保最终值在 32 位有符号整数范围内。

### 解题步骤详解
1.  初始化 `index = 0` 以跟踪字符串 `s` 中的当前位置。
2.  初始化 `sign = 1`（默认假定为正）。
3.  初始化 `result = 0`（用于构建数值，在构建过程中视为正数）。
4.  获取字符串的长度 `n = s.length()`。如果 `n` 为 0，则返回 0。
5.  **步骤 1：跳过前导空白。** 当 `index < n` 且 `s.charAt(index) == ' '` 时，递增 `index`。
6.  **步骤 2：检查符号。** 如果 `index < n`：如果 `s.charAt(index) == '+'`，则递增 `index`。否则，如果 `s.charAt(index) == '-'`，则设置 `sign = -1`，并递增 `index`。
7.  **步骤 3、4 和 5：读取数字、转换并处理溢出/截断。** 当 `index < n` 且字符 `s.charAt(index)` 是数字时循环：
    a.  将字符转换为整数：`digit = s.charAt(index) - '0'`。
    b.  **溢出检查（乘法之前）：** 检查 `result` 是否已大于 `Integer.MAX_VALUE / 10`。或者，如果 `result` 等于 `Integer.MAX_VALUE / 10` 且 `digit` 大于 `Integer.MAX_VALUE % 10`（即 7）。如果这些条件中的任何一个为真，则会发生溢出。如果 `sign` 为 `1`，则返回 `Integer.MAX_VALUE`。如果 `sign` 为 `-1`，则返回 `Integer.MIN_VALUE`。
    c.  更新 `result`：`result = result * 10 + digit`。
    d.  递增 `index`。
8.  **步骤 6：返回带有正确符号的最终结果。** 返回 `result * sign`。

### Java 代码实现
```java
class Solution {
    // 方法：将字符串转换为 32 位有符号整数 (atoi)
    public int myAtoi(String s) {
        // 处理 null 或空字符串输入
        if (s == null || s.length() == 0) {
            return 0; // 根据典型的 atoi 行为或问题约束
        }

        int index = 0;         // 指向字符串中当前字符的指针
        int n = s.length();    // 字符串的长度
        int sign = 1;          // 1 表示正数，-1 表示负数
        int result = 0;        // 存储累积的数值（作为正数）

        // 1. 跳过前导空白字符
        while (index < n && s.charAt(index) == ' ') {
            index++;
        }

        // 如果字符串仅包含空白，或者在修剪后为空
        if (index == n) {
            return 0;
        }

        // 2. 检查可选的符号字符（'+' 或 '-'）
        if (s.charAt(index) == '+') {
            // 符号为正（已为默认值），移动到下一个字符
            index++;
        } else if (s.charAt(index) == '-') {
            // 符号为负
            sign = -1;
            // 移动到下一个字符
            index++;
        }

        // 3. 读取数字，直到遇到非数字字符或字符串末尾
        while (index < n && Character.isDigit(s.charAt(index))) {
            // 将字符数字转换为整数值
            int digit = s.charAt(index) - '0';

            // 4. 在更新结果之前处理上溢/下溢和截断
            // 检查 `result * 10 + digit` 是否会溢出 Integer.MAX_VALUE
            // `Integer.MAX_VALUE` 是 2147483647。其最后一位数字是 7。
            if (result > Integer.MAX_VALUE / 10 ||
                (result == Integer.MAX_VALUE / 10 && digit > Integer.MAX_VALUE % 10) ) {
                // 如果发生溢出，则根据符号截断为 MAX_VALUE 或 MIN_VALUE
                return (sign == 1) ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            // （上面的检查正确处理了正负两种情况，
            // 因为我们将 `result` 构建为正数，并且截断
            // 在检测到溢出时会考虑符号。）

            // 将当前数字附加到结果中
            result = result * 10 + digit;
            // 移动到下一个字符
            index++;
        }

        // 5. 将符号应用于结果
        return result * sign;
    }
}
```

### 核心数据结构
无，主要使用整型变量来表示索引、符号和结果，以及从输入字符串中访问字符。

### 主要算法思想
字符串解析：该解决方案仔细地逐个字符遍历字符串，在每个步骤应用特定规则。状态管理：使用诸如 `index`、`sign` 和 `result` 之类的变量来维护解析过程的当前状态。边界情况处理：必须考虑许多边界情况：空/null 字符串、空白、符号、非数字字符，尤其是需要截断的整数上溢/下溢。

### 相关问题
*   整数反转 (LeetCode #7)
*   有效数字 (LeetCode #65)
*   基本计算器 (LeetCode #224)
```
