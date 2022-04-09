> 给你两个整数数组 nums1 和 nums2 ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。



### 思路

- 统计nums1每个元素出现的个数
- 遍历nums2，如果在hashmap可以找到，就加到res中
- 如果nums2元素出现的个数大于nums1，不加入res中



```golang
func f(nums1,nums2 []int) []int {
    hashMap := make(map[int]int)
    var res []int
    for i,v:= range nums1 {
        hashMap[v]++
    }
    for i,v := range nums2 {
        if hashMap[v] > 0 {
            res = append(res,v)
            hashMap[v]--
        }
    }
    return res
}
```