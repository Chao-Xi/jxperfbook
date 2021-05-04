# **10、Reverse String**

**String => list => string**

```

def main():
    str= "the sky is blue"
    s = str.split(" ")
    for i in range(len(s)-1,-1,-1):
        print(s[i],end=" ")

if __name__ == "__main__":
    main()
```

```
# output:
blue is sky the 
```

