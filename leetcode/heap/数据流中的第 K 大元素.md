> 设计一个找到数据流中第 k 大元素的类（class）。注意是排序后的第 k 大元素，不是第 k 个不同的元素。
> 请实现 KthLargest 类：
> KthLargest(int k, int[] nums) 使用整数 k 和整数流 nums 初始化对象。
> int add(int val) 将 val 插入数据流 nums 后，返回当前数据流中第 k 大的元素。



###  思路


- 初始化：循环调用add方法
- push：append KthLargest.IntSlice
- pop： get value && [:len(IntSlice)-1]
- add: call push && if > k pop element && return IntSlice[0]
