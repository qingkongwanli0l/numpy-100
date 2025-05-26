# 16. 最接近的三数之和 (3Sum Closest)

### 问题描述
给定一个长度为 `n` 的整数数组 `nums` 和一个整数 `target`，请在 `nums` 中找出三个整数，使得它们的和与 `target` 最接近。返回这三个整数的和。假定每组输入只存在唯一答案。

**示例：**
输入: `nums = [-1,2,1,-4]`, `target = 1`
输出: `2`
解释: 与 target 最接近的和是 2。`(-1 + 2 + 1 = 2)`。

限制条件:
*   `3 <= nums.length <= 1000`
*   `-1000 <= nums[i] <= 1000`
*   `-10^4 <= target <= 10^4`
[查看LeetCode问题描述](https://leetcode.cn/problems/3sum-closest/)

### 最优解法思路
此问题是“三数之和”的一个变种。目标是找到一个三元组，其和尽可能接近给定的 `target`。最有效的策略首先涉及对数组进行排序，这随后允许使用高效的双指针技术。
1.  **排序数组：** 对 `nums` 进行排序 (O(N log N))。
2.  **初始化跟踪变量：** `minDifference = Integer.MAX_VALUE`，`closestSum = 0`。
3.  **迭代并固定第一个元素：** 循环 `i` 从 `0` 到 `nums.length - 2`。`nums[i]` 是第一个元素。
4.  **双指针扫描：** 对于每个 `nums[i]`，使用 `left = i + 1` 和 `right = nums.length - 1`。当 `left < right` 时：
    a.  `currentSum = nums[i] + nums[left] + nums[right]`。
    b.  `currentDifference = Math.abs(currentSum - target)`。
    c.  如果 `currentDifference < minDifference`，则更新 `minDifference = currentDifference` 和 `closestSum = currentSum`。
    d.  如果 `currentSum < target`，则 `left++`（以增加和）。
    e.  否则如果 `currentSum > target`，则 `right--`（以减少和）。
    f.  否则（`currentSum == target`），返回 `currentSum`（完美匹配）。
5.  返回 `closestSum`。总体时间复杂度为 O(N^2)。

### 解题步骤详解
1.  对输入数组 `nums` 进行排序。
2.  初始化 `minDifference = Integer.MAX_VALUE`。
3.  初始化 `closestSum = 0`（此值将由第一个计算的和或任何更接近的和更新）。
4.  循环 `i` 从 `0` 到 `nums.length - 2`：
    a.  初始化 `left = i + 1`。
    b.  初始化 `right = nums.length - 1`。
    c.  当 `left < right` 时：
        i.   计算 `currentSum = nums[i] + nums[left] + nums[right]`。
        ii.  如果 `currentSum == target`，则立即返回 `target`。
        iii. 如果 `Math.abs(currentSum - target) < minDifference`：
             1. 更新 `minDifference = Math.abs(currentSum - target)`。
             2. 更新 `closestSum = currentSum`。
        iv. 如果 `currentSum < target`：递增 `left`。
        v.  否则（`currentSum > target`）：递减 `right`。
5.  返回 `closestSum`。

### Java 代码实现
```java
import java.util.Arrays; // Arrays.sort() 所需的导入

class Solution {
    // 方法：找出 nums[] 中三个整数的和与 target 最接近的值
    public int threeSumClosest(int[] nums, int target) {
        // 对数组进行排序以启用双指针方法
        Arrays.sort(nums);

        // 将 minDifference 初始化为可能的最大值
        // 这将存储目前找到的最小绝对差。
        int minDifference = Integer.MAX_VALUE;
        // 初始化 closestSum。这将存储与 target 具有 minDifference 的三元组的和。
        int closestSum = 0; 

        // 遍历数组，固定潜在三元组的第一个元素。
        // 我们只需要遍历到 nums.length - 2，因为我们还需要两个元素。
        for (int i = 0; i < nums.length - 2; i++) {
            // 为数组的其余部分初始化两个指针。
            int left = i + 1;          // 左指针从 nums[i] 之后开始
            int right = nums.length - 1; // 右指针从数组末尾开始

            // 使用双指针技术
            while (left < right) {
                // 计算当前三元组的和
                int currentSum = nums[i] + nums[left] + nums[right];

                // 如果当前和正好是 target，则这是最接近的可能。
                // 立即返回它。
                if (currentSum == target) {
                    return target;
                }

                // 计算当前和与 target 之间的绝对差
                int currentDifference = Math.abs(currentSum - target);

                // 如果当前差值小于目前找到的最小差值，
                // 则更新 minDifference 和 closestSum。
                if (currentDifference < minDifference) {
                    minDifference = currentDifference;
                    closestSum = currentSum;
                }
                
                // 调整指针以更接近 target
                if (currentSum < target) {
                    // 如果当前和小于 target，我们需要一个更大的和。
                    // 将左指针向右移动。
                    left++;
                } else { // currentSum > target
                    // 如果当前和大于 target，我们需要一个更小的和。
                    // 将右指针向左移动。
                    right--;
                }
            }
        }
        // 返回与 target 最接近的和
        return closestSum;
    }
}
```

### 核心数据结构
**数组 (`int[] nums`)：** 输入数组，进行原地排序。使用整型变量表示指针、`minDifference` 和 `closestSum`。

### 主要算法思想
**排序：** 一个预处理步骤 (O(N log N))，它使得高效的双指针技术成为可能。**双指针：** 一旦三元组的第一个数字固定，用于查找另外两个数字的核心逻辑。对于每个固定的第一个数字，此部分以 O(N) 运行。**贪心选择（指针移动）：** 根据当前和是小于还是大于目标来移动指针，始终尝试更接近目标和。

### 相关问题
*   三数之和 (LeetCode #15)
*   两数之和 (LeetCode #1)
*   两数之和 II - 输入有序数组 (LeetCode #167)
```
