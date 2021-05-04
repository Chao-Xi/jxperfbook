# **1、Apple Interview(3Sum)**

## 1 Three Sum

Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? 

**Find all unique triplets in the array which gives the sum of zero.**


### **My Solution**

```
def main(arr,n):
    found = False
    raw_results = {}
    results = {}
    for i in range(0, n-2):
        for j in range(i+1, n-1):
            for k in range(j+1, n):
                if (arr[i] + arr[j] + arr[k] == 0):
                    raw_results.update({i:sorted([arr[i],arr[j],arr[k]])})
        
    
    for key,value in raw_results.items():
        if value not in results.values():
            results[key] = value    

    return results

if __name__ == "__main__":
    arr = [0, 0, -1, -1, 2, -3, 1, 1]
    n = len(arr)
    print(main(arr,n))     
```

```
{0: [-1, 0, 1], 2: [-1, -1, 2], 4: [-3, 1, 2]}
```


### Remove duplicate from dictionary

```
result = {}

for key,value in raw_results.items():
        if value not in results.values():
            results[key] = value  
```

## 2 Maximum Distance in Arrays

```
"""
Input: 
[[1,2,3],
 [4,5],
 [1,2,3]]
Output: 4
Explanation: 
One way to reach the maximum distance 4 is to pick 1 in the first or third array and pick 5 in the second array.
"""
```

```
# Given m arrays, and each array is sorted in ascending order. 
# Now you can pick up two integers from two different arrays (each array picks one) 
# and calculate the distance. We define the distance between two integers a and b to be their absolute difference |a-b|. 
# Your task is to find the maximum distance.

"""
Input: 
[[1,2,3],
 [4,5],
 [1,2,3]]
Output: 4
Explanation: 
One way to reach the maximum distance 4 is to pick 1 in the first or third array and pick 5 in the second array.
"""


class Solution(object):
    def maxDistance(self, nums):
    # """
    # :type arrays: List[List[int]]
    # :rtype: int
    # """  
        low = float('inf')
        high = float('-inf')
        res = 0
        for num in nums:
            # We can use num[0] && num[-1] only because these lists are sorted
            res = max(res, max(high - num[0], num[-1] - low))
            low = min(low, min(num))
            high = max(high, max(num))
        # return low
        return res


list = [[1,2,3],[4,5], [1,2,3]]
s = Solution()
print(s.maxDistance(list)) 

#  python 1mda_624.py 
# 4
```