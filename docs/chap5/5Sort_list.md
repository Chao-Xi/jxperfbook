# **5、Reverse Sort List**


## Reverse sort from start to end

```
def main():
    a = [1,2,3,4,5]
    b = []
    
    for i in range(0,len(a)):
        b.append(i)
    
    for i in range(0,len(a)):
        b[i] = a[len(a)-i-1]
    return b

if __name__ == '__main__':
    print(main())
```

```
# output:
[5, 4, 3, 2, 1]
```

if no `b.append(i)`

```
IndexError: list assignment index out of range
```

## Reverse sort from start to half

```
def main():
    a = [1,2,3,4,5]
    tmp = 0

    for i in range(0,round(len(a)/2)):
        tmp = a[i]
        a[i] = a[len(a)-i-1]
        a[len(a)-i-1]=tmp
    
    return a
    
if __name__ == '__main__':
    print(main())
    a = [1,2,3,4,5]
```

```
tmp = a[i]
a[i] = a[len(a)-i-1]
a[len(a)-i-1]=tmp
```

## Sort function

```
b = sorted(a, reverse=True)
print(b)
```


