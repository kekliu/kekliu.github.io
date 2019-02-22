---
layout:     post
title:      "C++ lower_bound 函数妙用"
subtitle:   ""
date:       2019-02-22 22:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


### 1. 求下界
lower_bound函数的直接功能是求下界，下面是函数定义，其功能为返回第一个满足`target<=nums[i]`的`i`，若没找到，返回`nums.size()`。
```
// find minimum i where nums[i]>=target in [0, nums.size())
// warning: this function might return nums.size()
int lower_bound(vector<int> nums, int target) {
  int left = 0, right = nums.size();
  while (left < right) {
    int mid = left + (right - left) / 2;
    // change "<" to "<="
    // if you want to find minimum i where v[i]>target
    if (nums[mid] < target) left = mid + 1;
    else right = mid;
  }
  // return right as you wish
  return left;
}
```

### 2. 求上界
lower_bound函数还可以用于求上界，下面的函数功能为返回最后一个满足`nums[i]<target`的`i`，若没找到，返回`-1`。
```
// find maximum i where nums[i]<target
int upper_bound(vector<int> nums, int target) {
  return lower_bound(nums, target) - 1;
}
```

### 3. 二分查找
#### 3.1 普通二分查找
查找成功返回i，否则返回-1。
```
int bin_search(vector<int> nums, int target) {
  int i = lower_bound(nums, target);
  return (i == nums.size() || nums[i] != target) ? -1 : i;
}
```
#### 3.2 查找值在数组中第一次出现的地方
同上。
#### 3.3 查找值在数组中最后一次出现的地方
查找成功返回i，否则返回-1。
```
int bin_search_last(vector<int> nums, int target) {
  // warning: the lower_bound should use "<=" instead of "<" above
  int i = lower_bound(nums, target) - 1;
  return (i == -1 || nums[i] != target) ? -1 : i;
}
```
