# lower_bound函数妙用
## 求下界
lower_bound函数的直接功能是求下界，下面是函数定义，其功能为返回第一个满足`target<=nums[i]`的`i`，若没找到，返回`nums.size()`。
```cpp
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
## 求上界
lower_bound函数还可以用于求上界，下面的函数功能为返回最后一个满足`nums[i]<target`的`i`，若没找到，返回`-1`。
```cpp
// find maximum i where nums[i]<target
int upper_bound(vector<int> nums, int target) {
  return lower_bound(nums, target) - 1;
}
```
## 二分查找
### 普通二分查找
```cpp
int bin_search(vector<int> nums, int target) {
  int res = lower_bound(nums, target);
  if(res == nums.size()) return -1;
}
```
### 查找值在数组中第一次出现的地方
同上。
### 查找值在数组中最后一次出现的地方
```cpp
int bin_search_last(vector<int> nums, int target) {
  // warning: the lower_bound should use "<=" instead of "<" above
  int res = lower_bound(nums, target) - 1;
  if(res == nums.size()) return -1;
}
```
