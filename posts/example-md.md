# 示例：Markdown 题目

## 题目描述

给定一个整数数组 `nums` 和一个目标值 `target`，找出数组中和为目标值的两个数的索引。

## 示例

```
nums = [2, 7, 11, 15], target = 9
返回 [0, 1]，因为 nums[0] + nums[1] = 2 + 7 = 9
```

## 思路

使用哈希表存储已遍历的数及其索引，一次遍历即可找到答案。

## 代码

```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        diff = target - num
        if diff in seen:
            return [seen[diff], i]
        seen[num] = i