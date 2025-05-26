# 4. 寻找两个正序数组的中位数 (Median of Two Sorted Arrays)

### 问题描述
给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 中位数 。

算法的时间复杂度应该为 O(log (m+n)) 。
[查看LeetCode问题描述](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

### 最优解法思路
该问题要求在对数时间复杂度内找到两个已排序数组的中位数。这可以通过对较小数组的可能分区执行二分搜索来实现。目标是将 `nums1` 和 `nums2` 中的元素组合集划分为两个部分：左半部分和右半部分，使得左半部分中的所有元素都小于或等于右半部分中的所有元素。然后，中位数将从左半部分的最大元素和右半部分的最小元素中导出。设 `m` 为 `nums1` 的长度，`n` 为 `nums2` 的长度。如果 `m > n`，我们通过交换它们来确保 `nums1` 是较短的数组。这优化了二分搜索范围。二分搜索是在从 `nums1` 中为组合排序数组的左半部分提取的元素数量上执行的。设这个数量为 `partitionX`。因此，从 `nums2` 中为这个左半部分提取的元素数量将是 `partitionY = (m + n + 1) / 2 - partitionX`。`(m + n + 1) / 2` 中的 `+1` 正确处理了总长度为偶数和奇数的情况，确保左半部分要么具有相同数量的元素（对于偶数总数），要么多一个元素（对于奇数总数）。通过这些分区，我们确定了四个关键元素：`maxLeftX`：`nums1` 分区左侧的最大元素。`minRightX`：`nums1` 分区右侧的最小元素。`maxLeftY`：`nums2` 分区左侧的最大元素。`minRightY`：`nums2` 分区右侧的最小元素。（边缘情况，例如分区位于数组的最开始或最末尾，通过使用 `Integer.MIN_VALUE` 或 `Integer.MAX_VALUE` 来处理。）当 `maxLeftX <= minRightY` 且 `maxLeftY <= minRightX` 时，找到正确的分区。如果此条件成立且元素总数 `(m + n)` 为偶数，则中位数为 `(max(maxLeftX, maxLeftY) + min(minRightX, minRightY)) / 2.0`。如果 `(m + n)` 为奇数，则中位数为 `max(maxLeftX, maxLeftY)`。如果 `maxLeftX > minRightY`，则我们的 `partitionX` 太大（我们从 `nums1` 中提取了太多元素），因此我们将二分搜索调整到 `nums1` 当前搜索空间的左半部分。如果 `maxLeftY > minRightX`（这意味着 `maxLeftX <= minRightY` 为假，方式是 `partitionX` 太小），我们将二分搜索调整到右半部分。此二分搜索将继续进行，直到找到正确的分区。时间复杂度为 O(log(min(m,n)))，因为二分搜索是在较小的数组上执行的。空间复杂度为 O(1)。

### 解题步骤详解
1.  确定 `nums1` 的长度 `m` 和 `nums2` 的长度 `n`。
2.  为确保二分搜索在较小的数组上进行（为了效率），如果 `m > n`，则交换 `nums1` 和 `nums2`（以及 `m` 和 `n`）。
3.  初始化 `low = 0` 和 `high = m`，用于 `nums1` 上的二分搜索范围。
4.  只要 `low <= high` 就循环：
    a.  计算 `partitionX = low + (high - low) / 2`。这是从 `nums1` 中包含在组合排序集的左半部分的元素数量。
    b.  计算 `partitionY = (m + n + 1) / 2 - partitionX`。这是从 `nums2` 中用于左半部分的元素数量。
    c.  确定 `maxLeftX`：如果 `partitionX` 为 `0`，则为 `Integer.MIN_VALUE`；否则为 `nums1[partitionX - 1]`。
    d.  确定 `minRightX`：如果 `partitionX` 为 `m`（`nums1` 的所有元素都在左半部分），则为 `Integer.MAX_VALUE`；否则为 `nums1[partitionX]`。
    e.  确定 `maxLeftY`：如果 `partitionY` 为 `0`，则为 `Integer.MIN_VALUE`；否则为 `nums2[partitionY - 1]`。
    f.  确定 `minRightY`：如果 `partitionY` 为 `n`，则为 `Integer.MAX_VALUE`；否则为 `nums2[partitionY]`。
    g.  检查有效分区的条件：`maxLeftX <= minRightY && maxLeftY <= minRightX`。
        i.  如果为真：
            1.  如果 `(m + n) % 2 == 0`（总长度为偶数），则中位数为 `(Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0`。
            2.  否则（总长度为奇数），中位数为 `(double) Math.max(maxLeftX, maxLeftY)`。
            3.  返回计算出的中位数。
        ii. 如果 `maxLeftX > minRightY`：当前的 `partitionX` 太大。我们需要从 `nums1` 中减少元素。调整搜索范围：`high = partitionX - 1`。
        iii.否则 (`maxLeftY > minRightX`)：当前的 `partitionX` 太小。我们需要从 `nums1` 中增加元素。调整搜索范围：`low = partitionX + 1`。
5.  如果循环结束，则表示输入存在问题（例如，未排序），因为对于已排序的数组应始终能找到有效分区。在稳健的实现中，可能会抛出 `IllegalArgumentException`。

### Java 代码实现
```java
class Solution {
    // 方法：找出两个已排序数组的中位数
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        // 确保 nums1 是较短的数组，以优化二分搜索范围
        if (nums1.length > nums2.length) {
            // 如果 nums1 较长，则通过递归调用函数来交换它们
            return findMedianSortedArrays(nums2, nums1);
        }

        // 第一个数组（较短者）的长度
        int m = nums1.length;
        // 第二个数组（较长者）的长度
        int n = nums2.length;

        // nums1 中 partitionX 的二分搜索范围
        int low = 0;       // nums1 中元素的最小数量可以为 0
        int high = m;      // nums1 中元素的最大数量可以为 m (nums1 的全部)

        // 对 nums1 中的正确分区执行二分搜索
        while (low <= high) {
            // partitionX：组合数组左半部分中来自 nums1 的元素数量
            int partitionX = low + (high - low) / 2; // 避免与 (low+high)/2 相比发生溢出
            // partitionY：左半部分中来自 nums2 的元素数量
            // (m + n + 1) / 2 给出组合左半部分中的元素总数。
            // +1 正确处理了总长度为偶数和奇数的情况。
            int partitionY = (m + n + 1) / 2 - partitionX;

            // 确定定义分区边界的四个关键元素：
            // maxLeftX：nums1 中分区左侧的最大元素
            // 如果 partitionX 为 0，则左半部分中没有来自 nums1 的元素，因此使用 MIN_VALUE。
            int maxLeftX = (partitionX == 0) ? Integer.MIN_VALUE : nums1[partitionX - 1];
            // minRightX：nums1 中分区右侧的最小元素
            // 如果 partitionX 为 m，则 nums1 的所有元素都在左半部分，因此使用 MAX_VALUE。
            int minRightX = (partitionX == m) ? Integer.MAX_VALUE : nums1[partitionX];

            // maxLeftY：nums2 中分区左侧的最大元素
            int maxLeftY = (partitionY == 0) ? Integer.MIN_VALUE : nums2[partitionY - 1];
            // minRightY：nums2 中分区右侧的最小元素
            int minRightY = (partitionY == n) ? Integer.MAX_VALUE : nums2[partitionY];

            // 检查当前分区是否正确：
            // 左半部分中的所有元素必须小于或等于右半部分中的所有元素。
            // 这意味着 maxLeftX <= minRightY 且 maxLeftY <= minRightX。
            if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
                // 分区正确，计算中位数。
                // 如果元素总数 (m + n) 为偶数：
                if ((m + n) % 2 == 0) {
                    // 中位数是两个中间元素的平均值：
                    // 左半部分的最大值和右半部分的最小值。
                    return (Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0;
                } else {
                    // 如果元素总数为奇数：
                    // 中位数是左半部分中的最大元素。
                    return (double) Math.max(maxLeftX, maxLeftY);
                }
            } else if (maxLeftX > minRightY) {
                // partitionX 太大（来自 nums1 的元素对于左半部分来说太大了）。
                // 我们需要从 nums1 中提取更少的元素。
                // 将 partitionX 的二分搜索范围向左调整。
                high = partitionX - 1;
            } else { // 这意味着 maxLeftY > minRightX
                // partitionX 太小（来自 nums1 的元素太小了，
                // 这意味着我们需要从 nums1 中为左半部分提取更多元素，
                // 或者等效地，左半部分中来自 nums2 的元素太大了）。
                // 将 partitionX 的二分搜索范围向右调整。
                low = partitionX + 1;
            }
        }

        // 如果输入数组已排序且非空（根据此问题的典型 LeetCode 约束），
        // 则理想情况下不应到达此行。
        // 它表明存在问题，可能与假设或输入有效性有关。
        throw new IllegalArgumentException("输入数组未排序或输入存在其他问题。");
    }
}
```

### 核心数据结构
数组：输入的已排序数组 `nums1` 和 `nums2`。核心逻辑没有使用重要的额外数据结构，使得空间复杂度为 O(1)。

### 主要算法思想
二分搜索：算法的核心。它不是典型的值的二分搜索，而是对较小数组中正确分割点的二分搜索。分治法：通过对两个数组进行分区，概念上解决了问题。利用了已排序数组的属性来有效查找中位数。

### 相关问题
*   有序矩阵中第 K 小的元素 (LeetCode #378)
*   寻找第 K 小的距离对 (LeetCode #719)
*   分割数组的最大值 (LeetCode #410)
*   搜索旋转排序数组 (LeetCode #33)
```
