---
layout: post
title: "Search in rotated sorted array"
subtitle: "Leetcode 33"
thumbnail-img: /assets/img/algorithms.png
share-img: /assets/img/algorithms.png
tags: [algorithms]
---
More Leetcode practice. Let's take a break from all these hard-level problems and try something of medium difficulty.

## Problem statement
Given an array $$nums$$ after a possible rotation and an integer $$target$$, return the index of $$target$$ if it is in $$nums$$ and $$-1$$ if it is not in $$nums$$.

You must write an algorithm with $$O(log n)$$ runtime complexity.

## Solution
If we knew where the minimum element was in the rotated array, then we would be able to run a standard binary search on the sorted array (with the indexes shifted). So we just have to binary search for the minimum element. That was simple; why was this listed as a medium-level problem again?

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        n = len(nums)
        def get_least_index(nums):
            if nums[0] <= nums[n-1]: # this condition is not strictly necessary but it greatly speeds up the testcases :)
                return 0
            lo, hi = 1, n-1
            while lo < hi:
                mid = (lo+hi)//2
                if nums[mid] < nums[hi]:
                    hi = mid
                else:
                    lo = mid+1
            return lo

        k = get_least_index(nums)
        lo, hi = 0, n-1
        while lo <= hi:
            mid = (lo+hi)//2
            i = (mid + k)%n
            if nums[i] == target:
                return i
            if nums[i] < target:
                lo = mid+1
            else:
                hi = mid-1
        return -1
```

This submission scores in the top 95 and 85 percentiles for time and space complexity, respectively. I could probably reduce memory further if I didn't write a separate function for the first binary search, but I like writing conceptually clean programs.

### Reflection
While the high-level algorithm for this problem was pretty obvious, I suppose that writing it up provided a nice exercise in using precise, logical language. For instance, when searching for the minimum element of the array, I might think: "We know that the minimum element lies in the range $$[lo, hi]$$ inclusive. Therefore, when the middle element is less than the high element, I would know that the minimum element can lie in the middle, or possibly to the left. However, when the middle element is greater than the high element, then I know that the minimum element must lie *strictly* to its right. I should update my search range accordingly.

"Moreover, the minimum element is guaranteed to exist, so we should terminate the loop when the possible range includes only one element. This happens when the low and high ends of our range are the same." By precisely defining the meaning of the variables $$lo$$ and $$hi$$, we can reason quickly about our program and avoid silly off-by-one errors or infinite loops.

I would also note that it's important to read the entire problem carefully. In particular, the first binary search only works because we know that the array contains distinct values. If all but one of the elements were the same number, then we wouldn't be able to get a hint about where the minimum element should be, and then we'd be stuck. (I think I fell into that pit of confusion when I tried this problem for the first time, a few years ago.)