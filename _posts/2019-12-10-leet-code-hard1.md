---
layout: post
title: "LeetCode Hard 1"
description: "alg,python3,leetcode"
tags: [leetcode,python3]
image:
  path: /images/abstract-5.jpg
  feature: abstract-5.jpg
---


#### 周二算法题

> 先看看leetcode上第一道标记为Hard的题

*Median of Two Sorted Arrays*

There are two sorted arrays nums1 and nums2 of size m and n respectively.
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
You may assume nums1 and nums2 cannot be both empty.

```
Example 1:

nums1 = [1, 3]
nums2 = [2]

The median is 2.0
Example 2:

nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

就是找中位数，有点像，某个班级，男生一列，女生一列，都是按照从身高生序排列,要找出全班的身高的中位数。

以下就是我写的几个方法,思路都差不多，从两个数列中取出前n个数,假设n就是连个数列长度之和的一半。废话不多说，直接上代码
```
#!/usr/bin/python3

class Solution:
    def findMedianSortedArrays(self, nums1:List[int],nums2:List[int]) ->float:
        nums = nums1 + nums2
        nums.sort()
        n = len(nums)
        if n % 2 == 0:
            return (nums[n//2]+nums[n//2-1])/2
        else:
            return nums[n//2] 

    def findMedianSortedArraysNew(self, nums1:List[int],nums2:List[int]) ->float:
        n1 = len(nums1)
        n2 = len(nums2)    
        n = (n1+n2)//2
        i = 0
        pre = 0
        res = 0
        while i<=n :
            pre = res
            if len(nums1) == 0:
                res = nums2.pop()
            elif len(nums2) == 0:
                res = nums1.pop()
            elif nums1[-1] >=  nums2[-1]:
                res = nums1.pop()
            else:
                res = nums2.pop()
            i = i+1
        if (n1+n2) % 2 == 0:
            return    (pre+res)/2
        else:
            return pre 

    def findMedianSortedArraysNew2(self, nums1:List[int], nums2:List[int]) -> float:
        n1 = len(nums1)
        n2 = len(nums2)    
        n = (n1+n2)//2
        i = 0
        pre = 0
        res = 0
        j,k = 0,0
        while i<=n :
            pre = res
            if n1  == j:
                res = nums2[k]
                k = k+1
            elif n2 == k:
                res = nums1[j]
                j = j +1
            elif nums1[j] <=  nums2[k]:
                res = nums1[j]
                j = j +1
            else:
                res = nums2[k]
                k = k +1
            i = i+1
        if (n1+n2) % 2 == 0:
            return    (pre+res)/2
        else:
            return res        
```

本以为第一个方法是偷懒的方法(用到了系统的方法sort),效果可能不好；没想到后面两个自己写的方法，还不如第一个。

    方法1  88 ms   12.9 MB python3
    方法2  100 ms  12.9 MB python3
    方法3  96 ms   12.8 MB python3

所以，查了以下python3中sort方法的实现，发现它用到了一个名为Timsort的算法
恩，关于这个算法，打算再写几篇文章。今天算是把注册很久的账号激活了，刷刷算法题，防止脑袋生锈。
    
    https://leetcode.com/problems/median-of-two-sorted-arrays/

```
Runtime: 96 ms, faster than 88.25% of Python3 online submissions for Median of Two Sorted Arrays.
Memory Usage: 12.8 MB, less than 100.00% of Python3 online submissions for Median of Two Sorted Arrays.
```
