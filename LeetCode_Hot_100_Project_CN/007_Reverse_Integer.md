# 7. 整数反转 (Reverse Integer)

### 问题描述
给定一个带符号的 32 位整数 `x`，将其数字反转。如果反转 `x` 导致值超出带符号的 32 位整数范围 `[-2^31, 2^31 - 1]`，则返回 `0`。假设环境不允许存储 64 位整数。
[查看LeetCode问题描述](https://leetcode.cn/problems/reverse-integer/)

### 最优解法思路
该问题要求反转一个 32 位有符号整数的数字，同时严格遵守 32 位整数范围，并且不使用 64 位整数进行中间结果计算。核心思想是从数字的末尾提取数字并构建反转后的数字。我们可以通过重复取 `x` 的最后一位数字（使用 `x % 10`）并将其附加到我们的 `reversed_x` 中来实现。然后，通过移除 `x` 的最后一位数字（使用 `x / 10`）来更新 `x`。关键的挑战是在我们执行 `reversed_x = reversed_x * 10 + digit` 时，在实际发生溢出 *之前* 检测到潜在的溢出。设 `Integer.MAX_VALUE` 为 `2147483647`，`Integer.MIN_VALUE` 为 `-2147483648`。即将发生的上溢：如果 `reversed_x` 已经大于 `Integer.MAX_VALUE / 10`（即 `214748364`），那么任何进一步乘以 10 的操作肯定会导致上溢。如果 `reversed_x` 正好等于 `Integer.MAX_VALUE / 10`，那么如果下一个要添加的 `digit` 大于 `7`，将会发生上溢。即将发生的下溢：类似地，如果 `reversed_x` 小于 `Integer.MIN_VALUE / 10`（即 `-214748364`），那么乘以 10 将导致下溢。如果 `reversed_x` 正好等于 `Integer.MIN_VALUE / 10`，那么如果下一个 `digit` 小于 `-8`，将会发生下溢。如果这些条件中的任何一个表明即将发生上溢/下溢，我们应该返回 `0`。

### 解题步骤详解
1.  初始化 `reversed_x = 0`。此变量将存储数字反转后的整数。
2.  开始一个循环，只要 `x` 不为 `0` 就继续。
    a.  提取 `x` 的最后一位数字：`digit = x % 10`。
    b.  从 `x` 中移除最后一位数字：`x = x / 10`。
    c.  **在更新 `reversed_x` 之前执行上溢/下溢检查：**
        i.  检查正向上溢：如果 `reversed_x > Integer.MAX_VALUE / 10` 或（`reversed_x == Integer.MAX_VALUE / 10` 且 `digit > 7`），则操作 `reversed_x * 10 + digit` 将会导致上溢。返回 `0`。
        ii. 检查负向上溢（下溢）：如果 `reversed_x < Integer.MIN_VALUE / 10` 或（`reversed_x == Integer.MIN_VALUE / 10` 且 `digit < -8`），则操作 `reversed_x * 10 + digit` 将会导致下溢。返回 `0`。
    d.  将 `digit` 附加到 `reversed_x`：`reversed_x = reversed_x * 10 + digit`。
3.  一旦循环结束（当 `x` 变为 `0` 时），`reversed_x` 将保存有效的 32 位范围内的反转整数。返回 `reversed_x`。

### Java 代码实现
```java
class Solution {
    // 方法：反转一个 32 位有符号整数 x 的数字
    public int reverse(int x) {
        // 此变量将存储反转后的整数
        int reversed_x = 0;

        // 只要 x 不为 0，循环就继续
        while (x != 0) {
            // 从 x 中弹出最后一位数字
            int digit = x % 10;
            // 从 x 中移除最后一位数字
            x /= 10;

            // 在乘法和加法之前检查即将发生的正向上溢
            // Integer.MAX_VALUE 是 2147483647
            // 如果 reversed_x 已经大于 214748364 (MAX_VALUE / 10)，
            // 那么 reversed_x * 10 肯定会溢出。
            if (reversed_x > Integer.MAX_VALUE / 10) {
                return 0; // 上溢
            }
            // 如果 reversed_x 正好是 214748364，
            // 那么如果下一位数字大于 7，将会发生上溢。
            if (reversed_x == Integer.MAX_VALUE / 10 && digit > 7) {
                return 0; // 上溢
            }

            // 在乘法和加法之前检查即将发生的负向上溢（下溢）
            // Integer.MIN_VALUE 是 -2147483648
            // 如果 reversed_x 已经小于 -214748364 (MIN_VALUE / 10)，
            // 那么 reversed_x * 10 肯定会下溢。
            if (reversed_x < Integer.MIN_VALUE / 10) {
                return 0; // 下溢
            }
            // 如果 reversed_x 正好是 -214748364，
            // 那么如果下一位数字小于 -8，将会发生下溢。
            // (注意：对于负数，x%10 可能是负数，例如 -123 % 10 = -3)
            if (reversed_x == Integer.MIN_VALUE / 10 && digit < -8) {
                return 0; // 下溢
            }

            // 如果没有即将发生的上溢/下溢，则将数字推入 reversed_x
            reversed_x = reversed_x * 10 + digit;
        }

        // 返回反转后的整数
        return reversed_x;
    }
}
```

### 核心数据结构
除了用于存储 `x`、`reversed_x` 和 `digit` 的标准整型变量外，不需要其他核心数据结构。

### 主要算法思想
数字操作：解决方案的核心涉及使用模运算符 (`%`) 提取数字和使用整数除法 (`/`) 移除数字。溢出预防：在溢出/下溢发生之前，针对 `Integer.MAX_VALUE` 和 `Integer.MIN_VALUE`（除以 10）执行关键检查，以预测和防止溢出/下溢，确保解决方案保持在 32 位整数限制内。

### 相关问题
*   字符串转换整数 (atoi) (LeetCode #8)
*   回文数 (LeetCode #9)
```
