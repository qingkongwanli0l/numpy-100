# 13. 罗马数字转整数 (Roman to Integer)

### 问题描述
给定一个罗马数字字符串 `s`，将其转换为整数。罗马数字由七个不同的符号表示：I、V、X、L、C、D 和 M。
```
符号       值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```
通常，罗马数字从左到右按从大到小的顺序书写（例如，`XII` 代表 12，`XXVII` 代表 27）。但是，有六种情况使用减法：
*   `I` 可以放在 `V` (5) 和 `X` (10) 之前，构成 4 (`IV`) 和 9 (`IX`)。
*   `X` 可以放在 `L` (50) 和 `C` (100) 之前，构成 40 (`XL`) 和 90 (`XC`)。
*   `C` 可以放在 `D` (500) 和 `M` (1000) 之前，构成 400 (`CD`) 和 900 (`CM`)。
输入保证是 1 到 3999 范围内的有效罗马数字。
[查看LeetCode问题描述](https://leetcode.cn/problems/roman-to-integer/)

### 最优解法思路
从罗马数字字符串到整数的转换需要仔细处理减法表示法（其中较小值的符号先于较大值的符号表示减法，如 "IV" = 4）。一种常见且清晰的方法是从左到右遍历字符串。对于每个罗马符号，我们找到其整数值。然后我们查看字符串中的*下一个*符号。如果当前符号的值*小于*下一个符号的值，则表示这是一个减法对。在这种情况下，我们从运行总和中减去当前符号的值。否则（如果当前符号的值*大于或等于*下一个符号的值，或者它是字符串中的最后一个符号），则将其值简单地加到运行总和中。

### 解题步骤详解
1.  创建一个映射（例如，使用 `HashMap`）来存储每个罗马数字字符的整数值：'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000。
2.  初始化一个整型变量 `totalInteger = 0`。
3.  使用索引 `i` 从 `0` 到 `s.length() - 1` 遍历输入罗马数字字符串 `s`。
    a.  获取当前罗马符号 `s.charAt(i)` 的整数值，设为 `currentValue`。
    b.  检查字符串中是否存在下一个符号（即，如果 `i + 1 < s.length()`）。
        i.  如果存在下一个符号，获取其整数值。设为 `nextValue`。
        ii. 比较 `currentValue` 和 `nextValue`：如果 `currentValue < nextValue`（例如，"IV" 中的 'I' 在 'V' 之前），则从 `totalInteger` 中减去 `currentValue`。否则（`currentValue >= nextValue`），将 `currentValue` 加到 `totalInteger`。
    c.  否则（如果 `s.charAt(i)` 是字符串中的最后一个符号），将 `currentValue` 加到 `totalInteger`。
4.  遍历完所有符号后，`totalInteger` 将保存最终的整数表示。返回 `totalInteger`。

### Java 代码实现
```java
import java.util.HashMap; // HashMap 所需的导入
import java.util.Map;     // Map 接口所需的导入

class Solution {
    // 方法：将罗马数字字符串转换为整数
    public int romanToInt(String s) {
        // 创建一个映射来存储罗马数字符号的整数值
        Map<Character, Integer> romanValues = new HashMap<>();
        romanValues.put('I', 1);
        romanValues.put('V', 5);
        romanValues.put('X', 10);
        romanValues.put('L', 50);
        romanValues.put('C', 100);
        romanValues.put('D', 500);
        romanValues.put('M', 1000);

        // 初始化总整数值为 0
        int totalInteger = 0;
        // 获取罗马数字字符串的长度
        int n = s.length();

        // 从左到右遍历字符串
        for (int i = 0; i < n; i++) {
            // 获取当前罗马符号的整数值
            int currentValue = romanValues.get(s.charAt(i));

            // 检查字符串中是否存在下一个符号
            if (i + 1 < n) {
                // 获取下一个罗马符号的整数值
                int nextValue = romanValues.get(s.charAt(i + 1));

                // 如果当前符号的值小于下一个符号的值，
                // 则是减法情况（例如，"IV"，其中 I=1, V=5。1 < 5）。
                if (currentValue < nextValue) {
                    totalInteger -= currentValue; // 减去当前值
                } else {
                    // 否则，是加法情况（例如，"VI"，其中 V=5, I=1。5 >= 1）。
                    totalInteger += currentValue; // 加上当前值
                }
            } else {
                // 如果这是字符串中的最后一个符号，则始终加上其值。
                totalInteger += currentValue;
            }
        }

        // 返回最终计算出的整数值
        return totalInteger;
    }
}
```

### 核心数据结构
**HashMap：** 用于存储从罗马数字字符（例如，'I'、'V'）到其相应整数值（例如，1、5）的映射。这允许对符号的值进行高效的 O(1) 查找。

### 主要算法思想
**字符串遍历与先行查看：** 该算法遍历字符串，对于每个字符，它通常会查看下一个字符以确定当前字符是否是减法对的一部分。**条件逻辑：** 根据当前符号值与下一个符号值的比较，算法决定是从总数中加上还是减去当前符号的值。

### 相关问题
*   整数转罗马数字 (LeetCode #12)
```
