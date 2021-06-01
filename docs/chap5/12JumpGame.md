# **12、Jump Game**

Given an array of non-negative integers, you are at the first position of the array. Every number of this array indicates the max steps you can walk.  Please write a piece of code (prefer python) to determine if you can reach the final position.


For example:


Given [2, 0, 1], you can firstly walk maximum 2 steps and reach position 3 (value is 1 in this case), which is the final position. You should return True for this case. 


Given [1, 1, 0, 4], you can't reach the final position, because you can only reach position 3 (value is 0).

```
def main(nums):
    reach = 0
    for i, num in enumerate(nums):
        if i > reach:
            return False
        reach = max(reach, i + int(num))
    return True

if __name__ == "__main__":
    # nums = [2, 0, 1]
    nums = [1, 1, 0, 4]
    print(main(nums))     
```

```
python3 test2.py 
False
```