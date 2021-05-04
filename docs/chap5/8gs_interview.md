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