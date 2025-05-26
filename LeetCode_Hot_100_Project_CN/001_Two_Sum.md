# 1. 两数之和 (Two Sum)

### 问题描述
给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 和为目标值 `target` 的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

[查看问题描述](https://leetcode.cn/problems/two-sum/)

### 最优解法思路
解决此问题的最有效方法是使用哈希映射。我们遍历数组一次。对于每个元素，我们计算其“补数”（达到目标所需的值）。然后我们检查此补数是否已存在于哈希映射中。如果在哈希映射中找到补数，则意味着我们找到了加起来等于目标的两个数字。然后我们可以返回它们的索引。如果未找到补数，我们会将当前元素及其索引添加到哈希映射中，以便可以根据将来的元素进行检查。此方法可确保我们只需要遍历数组一次，从而使平均时间复杂度为 O(n)，因为哈希映射操作（插入和查找）平均需要 O(1) 时间。空间复杂度为 O(n)，用于将元素存储在哈希映射中。

### 解题步骤详解
1.  创建一个空的哈希映射。此映射将存储数组中的数字作为键，并将其对应的索引作为值（例如，`Map<Integer, Integer> numMap = new HashMap<>();`）。
2.  从第一个元素到最后一个元素遍历输入数组 `nums`，同时记录当前索引（我们称之为 `i`）。
3.  对于数组中的每个元素 `nums[i]`：
    a.  计算达到目标所需的补数：`complement = target - nums[i]`。
    b.  检查此 `complement` 是否已作为键存在于哈希映射中 (`numMap.containsKey(complement)`)。
        i.  如果存在，则表示补数（即先前遇到的数组中的另一个数字）和当前数字 `nums[i]` 加起来等于 `target`。映射中与 `complement` 键关联的值是其索引 (`numMap.get(complement)`)，当前数字的索引是 `i`。返回这两个索引。
        ii. 如果不存在，则表示我们尚未找到该配对。将当前数字 `nums[i]` 及其索引 `i` 添加到哈希映射中：`numMap.put(nums[i], i)`。这将存储当前数字以供将来检查。
4.  （问题陈述保证只有一个解决方案，因此循环应始终找到并返回一个配对。如果没有保证解决方案，则可能需要在循环后处理不存在此类配对的情况，例如通过引发异常。）

### Java 代码实现
```java
// 导入 Java 工具类，用于实现哈希映射功能
import java.util.HashMap; // 导入 HashMap 类，它提供了哈希表数据结构的实现，用于存储键值对
import java.util.Map;      // 导入 Map 接口，它是 HashMap 实现的接口，定义了映射类型操作的规范

// 定义名为 Solution 的类，通常用于封装 LeetCode 问题的解法
class Solution {
    // 定义 public 方法 twoSum，它接受一个整数数组 nums 和一个整数目标值 target 作为输入
    // 目的是在数组中找到两个数，它们的和等于 target，并返回这两个数的索引
    public int[] twoSum(int[] nums, int target) {
        // 创建一个 HashMap 实例，用于存储数组中的数字及其对应的索引
        // 键 (Integer) 是数组中的数字，值 (Integer) 是该数字在数组中的索引
        Map<Integer, Integer> numMap = new HashMap<>();

        // 开始一个 for 循环，遍历输入数组 nums 中的每一个元素
        // 变量 i 是当前元素的索引，从 0 开始，直到数组的最后一个元素的索引 (nums.length - 1)
        for (int i = 0; i < nums.length; i++) {
            // 计算当前元素 nums[i] 需要与哪个数相加才能等于目标值 target
            // 这个数被称为“补数” (complement)
            int complement = target - nums[i]; // 计算补数：target 减去当前数组元素

            // 检查 numMap (哈希映射) 中是否已经存在键为 complement 的条目
            // containsKey 方法用于判断映射中是否包含指定的键
            if (numMap.containsKey(complement)) { // 如果哈希映射中包含这个补数
                // 如果补数存在于 numMap 中，说明我们找到了符合条件的两个数
                // numMap.get(complement) 返回补数在数组中的索引
                // i 是当前元素的索引
                // 创建并返回一个包含这两个索引的新整数数组
                return new int[] { numMap.get(complement), i }; // 返回存储补数索引和当前元素索引的数组
            }
            // 如果在 numMap 中没有找到补数
            // 将当前元素 nums[i] 及其索引 i 存入 numMap 中
            // 这样，在后续的迭代中，如果遇到一个数，其补数是当前这个 nums[i]，就可以找到它
            numMap.put(nums[i], i); // 将当前数字 nums[i] 和其索引 i 放入哈希映射中
        }
        // 根据题目说明，每种输入只会对应一个答案，因此程序流程理论上不应该执行到这里
        // 如果代码执行到此处，表示在数组中没有找到任何两个数的和等于目标值 target
        // 这通常意味着输入不符合题目的假设，或者解法逻辑有误 (但在本题标准解法中，此行是为了完整性)
        throw new IllegalArgumentException("没有找到两数之和的解"); // 抛出非法参数异常，表示未找到满足条件的解
    }
}
```

### 核心数据结构
哈希映射（或哈希表）：用于存储数组中的数字及其索引，从而使查找和插入的平均时间复杂度为 O(1)。在 Java 中，这通常使用 `java.util.HashMap` 实现。

### 主要算法思想
哈希：核心思想是使用哈希映射快速检查补数是否存在。这避免了嵌套循环的需要（这会导致 O(n^2) 的时间复杂度）。

### 相关问题
*   三数之和 (LeetCode #15)
*   四数之和 (LeetCode #18)
*   两数之和 II - 输入有序数组 (LeetCode #167)
*   和为 K 的子数组 (LeetCode #560)
*   两数之和 III - 数据结构设计 (LeetCode #170)
*   两数之和 IV - 输入 BST (LeetCode #653)
```
