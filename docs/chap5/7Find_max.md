# **Find Maxmium number count in list and nth number**


## **1、Find Max number in list**

```
def main():
    a = [1,4,3]
    max_val = -1
    for i in range(0,len(a)):
        if a[i] > max_val:
            max_val = a[i]
    return max_val

if __name__ == "__main__":
    print(main())
```


* `max_val = -1`

```
for i in range(0,len(a)):
        if a[i] > max_val:
            max_val = a[i]
```

## **2、Find Max count in list**

```
def main():
    a = [1, 3, 4, 3, 4, 1, 3]
    time_tmp = 0
    time_max = 0 
    result={}

    for i in range(0,len(a)):
        for j in range(0,len(a)):
            if a[i] == a[j]:
                time_tmp += 1
        # result[a[i]] = time_tmp

    # return result

        if time_tmp > time_max:
            time_max = time_tmp
            val_max = a[i]
    
    return val_max
    
if __name__ == "__main__":
    print(main())
    
```

```
output:
3
```



## **2、Find Max count variable in list with dictionary**

```
a = [1, 2, 3, 4, 5, 5, 5, 6]

d = {}    # a[i]为key, count 为 value
result = []

for i in range(0,len(a)):
    if a[i] in d.keys():
        d[a[i]] = d.get(a[i])+1
    else:
        d[a[i]] = 1
    
    # val_max = -1
    # time_max = 0

for count in d.values():
    result.append(count)

# return result
print(max(result))
```

```
output:
3
```


## **3、Find Nth number in list**

在一个有序数组中，查找出第一个大于 9 的数字，假设一定存在。例如，`arr = { -1, 3, 3, 7, 10, 14, 14 }`; 

```
def main():
    targetNumb = 9
    arr = [ -1, 3, 3, 7, 10, 14, 14 ]
    middle = 0
    low = 0
    high = len(arr) - 1

    while low <= high:
        middle = round((high + low)/2)
        if arr[middle] > targetNumb and (middle == 0 or arr[middle-1] <= targetNumb):
            print(f"The first number bigger than {targetNumb} which is {arr[middle]}")
            break
        elif arr[middle] > targetNumb:
            # 说明该数在low~middle之间
            high = middle - 1
        else:
            #  说明该数在middle~high之间
            low = middle + 1


if __name__ == "__main__":
    main()
```

```
The first number bigger than 9 which is 10
```





