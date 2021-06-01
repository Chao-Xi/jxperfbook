# **11、Count how many zero in `N!`**

Count how many zero in `N!` . Please write a piece of code (prefer python) and explain the complexity of your solution (in big-O notation)

```
def main(n):
    
    # Init result
    count = 0
    
    while(n >= 5):
        n //= 5
        count += n

    return count

if __name__ == "__main__":
    n = 20 
    print(f'{n}! has {main(n)} 0')     
```

```
$ python3 test1.py 
20! has 4 0
```


Only one while loop, so the time complexity is O(n) and no pre-defined list the space complexity is O(0)

