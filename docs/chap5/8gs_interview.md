# **8、GS Interview**


```
Implement a run length encoding function.

* For a string input the function returns output encoded as follows:
*
* "a"     -> "a1"
* "aa"    -> "a2"
* "aabbb" -> "a2b3"
* "aabbbaa" -> "a2b3a2"
* ""      -> ""
```



```
def doTestsPass():
    # Assert(rle("aaa"),        "a3",           "Example 1" );
    # Assert(rle("aabbc"),      "a2b2c1",       "Example 5" );
    str = "aabbc"

    if len(str) == 1:
        print(f"{str}1")
    
    # sub = ""
    char_length = 0
    result = {}
    
    for char in str:
        if char not in result.keys():
            result[char] = 1    
        else:
            result[char] += 1
      
    
    # print(result)

    new_str = ''
    for key,value in result.items():
        print(f"{key}{value}",end ="")
        

if __name__ == "__main__":
    doTestsPass()  
```

```
#output
a2b2c1
```

### **Python – Extract distinct K length substrings**

```
def distinctSubstring(str, n):
    # Put all distinct substring in a HashSet
    result = set()
    for i in range(len(str)):
        for j in range(i + 1, len(str) + 1):
            if len(str[i:j:n]) > 1:
                result.add(str[i:j:n])
    return result

if __name__ == '__main__':
     
    str = "aaab";
    subs = distinctSubstring(str,2);
 
    print("Distinct Substrings are: ");
    for s in subs:
        print(s);
```

```
$ python3 24unqiuesub.py 
Distinct Substrings are: 
ab
aa
```

### **Python program to find minimum element in a sorted and rotated array**

```
def findMin(arr, low, high): 
    # This condition is needed to handle the case when array is not 
    # rotated at all 
    if high < low: 
        return arr[0] 
  
    # If there is only one element left 
    if high == low: 
        return arr[low] 
  
    # Find mid 
    mid = int((low + high)/2) 
  
    # Check if element (mid+1) is minimum element. Consider 
    # the cases like [3, 4, 5, 1, 2] 
    if mid < high and arr[mid+1] < arr[mid]: 
        return arr[mid+1] 
  
    # Check if mid itself is minimum element 
    if mid > low and arr[mid] < arr[mid - 1]: 
        return arr[mid] 
  
    # Decide whether we need to go to left half or right half 
    if arr[high] > arr[mid]: 
        return findMin(arr, low, mid-1) 
    return findMin(arr, mid+1, high) 
  
# Driver program to test above functions 
arr1 = [5, 6, 1, 2, 3, 4] 
n1 = len(arr1) 
print("The minimum element is " + str(findMin(arr1, 0, n1-1))) 
```