# **9、Find substring inside String**


### **9-1 Function 1: Double loop string**

**假设要从主串 s = "goodgoogle" 中找到 t = "google" 子串**

```
def main():
    s = "goodgoogle"
    t = "google"
    isFind = 0
    
    for i in range(0,len(s)-len(t)+1):
        if s[i] == t[0]:
            jc = 0
            for j in range(0,len(t)):
                if s[i+j] != t[j]:
                    break
                jc = j
        
        if jc == len(t)-1:
            isFind = 1 
    print(isFind)
```

* `s[i+j] != t[j]`: 不同了就break

```
if s[i+j] != t[j]:
	break
```

                    
### **9-2 Function 2: Python inner function: index()**

```
def main():
    s = "goodgoogle"
    t = "google"
    try:
        if s.index(t) > 0:
            print('Found')
    except ValueError:
        print('Not foud')

if __name__ == '__main__':
    main()
```

```
output:
Found
```
                    
### **9-3 Function 2: Python inner function: find()**

```
s = "goodgoogle"
t = "google"
print(s.find(t))
```

```
4
```



