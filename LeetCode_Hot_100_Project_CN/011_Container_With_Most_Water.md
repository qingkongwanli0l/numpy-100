# 11. 盛最多水的容器 (Container With Most Water)

### 问题描述
给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。
找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。返回容器可以储存的最大水量。
注意：你不能倾斜容器。
[查看LeetCode问题描述](https://leetcode.cn/problems/container-with-most-water/)

### 最优解法思路
该问题要求我们找到由给定 `height` 数组中的两条垂直线和 x 轴形成的最大面积。容器可以容纳的水量由其宽度和较短边的长度决定。具体来说，`面积 = 宽度 * min(左边高度, 右边高度)`。暴力破解方法将涉及检查每一对可能的线，导致 O(n^2) 的时间复杂度。更有效的解决方案使用**双指针**技术。
1.  初始化两个指针：`left` 指向 `height` 数组的开头（索引 0），`right` 指向数组的末尾（索引 `n-1`）。
2.  初始化一个变量 `maxWater` 为 0。
3.  只要 `left` 指针小于 `right` 指针，就循环：
    a.  计算 `currentWidth = right - left`。
    b.  确定 `currentHeight = Math.min(height[left], height[right])`。
    c.  计算 `currentArea = currentWidth * currentHeight`。
    d.  更新 `maxWater = Math.max(maxWater, currentArea)`。
    e.  **移动指针：** 为了最大化面积，我们需要更大的宽度或高度。随着指针向内移动，宽度减小。高度受限于较短的线。如果 `height[left] < height[right]`，则 `left` 线是限制因素。移动 `left++` 以期找到更高的左线。如果 `height[right] <= height[left]`，则 `right` 线是限制因素。移动 `right--` 以期找到更高的右线。
4.  一旦指针相遇，`maxWater` 将保存结果。

### 解题步骤详解
1.  初始化 `maxWater = 0`。
2.  初始化 `left = 0`（指向数组开头的指针）。
3.  初始化 `right = height.length - 1`（指向数组末尾的指针）。
4.  当 `left < right` 时：
    a.  计算较短线的高度：`h = Math.min(height[left], height[right])`。
    b.  计算宽度：`w = right - left`。
    c.  计算当前面积：`area = h * w`。
    d.  更新 `maxWater = Math.max(maxWater, area)`。
    e.  如果 `height[left] < height[right]`：递增 `left`。
    f.  否则（如果 `height[right] <= height[left]`）：递减 `right`。
5.  返回 `maxWater`。

### Java 代码实现
```java
class Solution {
    // 方法：找出容器可以储存的最大水量
    public int maxArea(int[] height) {
        // 初始化目前找到的最大水量为 0
        int maxWater = 0;
        // 初始化左指针指向数组的开头
        int left = 0;
        // 初始化右指针指向数组的末尾
        int right = height.length - 1;

        // 只要左指针在右指针的左边，就继续循环
        while (left < right) {
            // 确定较短线的高度，因为这限制了水位
            int currentHeight = Math.min(height[left], height[right]);
            // 计算两条线之间的宽度
            int currentWidth = right - left;
            // 计算此容器可以容纳的水的面积
            int currentArea = currentHeight * currentWidth;

            // 如果当前面积更大，则更新找到的最大水量
            maxWater = Math.max(maxWater, currentArea);

            // 尝试找到一个可能容纳更多水的配置。
            // 面积受限于较短的线。为了潜在地增加面积，
            // 我们必须尝试增加这条较短线的高度。
            // 所以，我们移动对应于较短线的指针。
            if (height[left] < height[right]) {
                // 如果左边的线较短，将其向右移动一步
                left++;
            } else {
                // 如果右边的线较短（或相等），将其向左移动一步
                right--;
            }
        }

        // 返回找到的最大水量
        return maxWater;
    }
}
```

### 核心数据结构
**数组 (`int[] height`)：** 输入数组，表示垂直线的高度。用于指针和跟踪最大水量的整型变量。不需要复杂的数据结构。

### 主要算法思想
**双指针：** 这是有效解决方案的核心。两个指针从数组的两端开始，并向中心收敛。**贪心方法：** 在每次迭代中，我们计算当前线对的面积并更新最大值。移动哪个指针的决定是贪心的：我们移动较短线的指针，因为移动较高线的指针只会减小宽度而不增加高度（高度受限于较短线）。此策略旨在找到更高的限制线。

### 相关问题
*   接雨水 (LeetCode #42)
*   两数之和 II - 输入有序数组 (LeetCode #167)
```
