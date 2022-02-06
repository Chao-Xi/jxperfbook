# **2、Python Interview**

## **1、String**


```
message = """Assassain Creed
Nothing is true, everthing is permitted"""

print(message)
print(len(message))


# output:
Assassain Creed
Nothing is true, everthing is permitted
55
```

```
a = [1,2,3,4]
b = 'sample string'

print(str(a))
print(repr(a))

print(str(b))
# sample string
print(repr(b))
# 'sample string'
```


### **1-1 slice**

```
print(message[10:15])
#first bracket include the letter, but the second does not

# Creed

print(message[:10])
# Assassain
```

### **1-2 func**

```
print(message.lower())
print(message.upper())

print('Count how many e inside the "message" ')
print(message.count('e'))
print(message.find('true')) 

# 7
# 27
```

```
new_message=message.replace("Nothing is true, everthing is permitted", "We work in dark and serve the light")
print(new_message)

# Assassain Creed
# We work in dark and serve the light
```

### **1-3 format**

```
greet_msg_format = '{}, {} Chao'.format(greet, name)
print(greet_msg_format)


greet_msg_f= f'{greet.upper()}, {name} Jacob'
print(greet_msg_f)
```

## **2、Digit**

```
num = 3
print (type(num))

num += 1
print (num)

print ("-3 abs is", abs (-3))
print ("3.75 round is",round(3.75))
print ("3.75 at 1 digit round is",round(3.75, 1))
```

```
# output
<class 'int'>
4
-3 abs is 3
3.75 round is 4
3.75 at 1 digit round is 3.8
```


## **3、List**

```
list = [" ", " ", " ", " " ," "]
AC_list = ['Origin', 'Revolution', 'Syndicate', 'Chronicles', 'Odyssey']
print (AC_list)
print ('Lenth of assassin creed list:', len(AC_list))  

print (AC_list[4])                              # Odyssey             
print (AC_list[-1])                             # Odyssey
print (AC_list[2:])                             # ['Syndicate', 'Chronicles', 'Odyssey']
```

```
['Origin', 'Revolution', 'Syndicate', 'Chronicles', 'Odyssey']
Lenth of assassin creed list: 5
Odyssey
Odyssey
['Syndicate', 'Chronicles', 'Odyssey']
```

```
my_list = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(my_list[5])
print(my_list[0:6])          #end is exclusive, start is inclusive
print(my_list[1:-2])

# output
5
[0, 1, 2, 3, 4, 5]
[1, 2, 3, 4, 5, 6, 7]


print(my_list[::-1])
# [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

print(my_list[-2::-1])
# [8, 7, 6, 5, 4, 3, 2, 1, 0]
``` 



### **3-1 List opeartion**

```
AC_list.append('Black flag')
print (AC_list)        

AC_list.insert(0, 'IV Black Flag')
print(AC_list)

AC_list_old = ['IV Black Flag', 'Rogue']
AC_list.extend(AC_list_old)
print('extend:', AC_list)

AC_list.remove('Odyssey')
print('remove:', AC_list)

AC_list2_popped = AC_list.pop()
print('poped:', AC_list)
# poped: ['IV Black Flag', 'Origin', 'Revolution', 'Syndicate', 'Chronicles', 'Black flag', 'IV Black Flag']
```

* append: `AC_list.append`
* insert: `AC_list.insert(0, 'IV Black Flag')`
* extend: `AC_list.extend(AC_list_old)`
* pop: `AC_list.pop()`
* remove: `AC_list.remove('Odyssey')`

### **3-2 List sort**

```
AC_list.reverse()
print('reverse:', AC_list)  

num_list = [0, 32, 98, 27, 19, 20]
num_list1 = [0, 32, 98, 27, 19, 20]
num_list2 = [0, 32, 98, 27, 19, 20]


num_list.sort()
print('sort method:', num_list)
# sort method: [0, 19, 20, 27, 32, 98]

num_list1.sort(reverse=True)
print('reverse sort:', num_list1)
# reverse sort: [98, 32, 27, 20, 19, 0]

sorted_list = sorted(num_list)
print('sorted method:',sorted_list)
```

```
sort method: [0, 19, 20, 27, 32, 98]
reverse sort: [98, 32, 27, 20, 19, 0]
sorted method: [0, 19, 20, 27, 32, 98]
```

### **3-3 In list or not**

```
print('Brotherhood in the list:', 'Brotherhood' in AC_list)
print('Chronicles in the list:', 'Chronicles' in AC_list)

# Brotherhood in the list: False
# Chronicles in the list: True
```

### **3-4 Loop the list**

```
for ac in AC_list:
    print('for loop:', ac)
    
    
for loop: IV Black Flag
for loop: Black flag
for loop: Chronicles
for loop: Syndicate
for loop: Revolution
for loop: Origin
for loop: IV Black Flag
```

```
for index, ac in enumerate(AC_list):
    print('index', index, 'in loop is', ac)

# output

index 0 in loop is IV Black Flag
index 1 in loop is Black flag
index 2 in loop is Chronicles
index 3 in loop is Syndicate
index 4 in loop is Revolution
index 5 in loop is Origin
index 6 in loop is IV Black Flag
```

```
for index, ac in enumerate(AC_list, start=1):
	print('index', index, 'in loop is', ac)

# output
index 1 in loop is IV Black Flag
index 2 in loop is Black flag
index 3 in loop is Chronicles
index 4 in loop is Syndicate
index 5 in loop is Revolution
index 6 in loop is Origin
index 7 in loop is IV Black Flag
```

### **3-5 Multiple list**

```
# u use any sign to concat the list
AC_list_Arrow = ' ->  '.join(AC_list)
print('join:', AC_list_Arrow )
```

`' ->  '.join(AC_list)`: `join: IV Black Flag ->  Black flag ->  Chronicles ->  Syndicate ->  Revolution ->  Origin ->  IV Black Flag`


```
print(type(AC_list_Arrow))

# output
<class 'str'>
```

```
new_AC_List = AC_list_Arrow.split(' -> ')
print('split:' ,new_AC_List )


# output
split: ['IV Black Flag', ' Black flag', ' Chronicles', ' Syndicate', ' Revolution', ' Origin', ' IV Black Flag']
```

## **4、Tuples**

### Tuples difference between list [immutable, assignment unsupported]

Tuples with () is immutable

```
tuple_1 = ('History', 'Math', 'Physics', 'CompSci')

tuple_1[0] = 'Art'
# TypeError: 'tuple' object does not support item assignment
```

## **5、Sets**


```
# Sets is another kind of list except the order of set always change 

AC_list = {'Origin', 'Revolution', 'Syndicate', 'Chronicles', 'Odyssey'}
print(AC_list)
```

```
# Remove duplicate by sets
s3 = {1, 2, 3 ,4, 5, 1, 2 ,3}
print(s3)
# {1, 2, 3, 4, 5}

s1 = {1, 2, 3 ,4, 5}
print(s1)
# {1, 2, 3, 4, 5}

s1.add(6)
print(s1)
# {1, 2, 3, 4, 5, 6}

s1.update([6, 7, 8])
print(s1)
# {1, 2, 3, 4, 5, 6, 7, 8}

s1.remove(8)  #8 is value in set
print(s1)
# {1, 2, 3, 4, 5, 6, 7}

s1.discard(8)
print(s1)
#{1, 2, 3, 4, 5, 6, 7}
```


* `add()`	Adds an element to the set
* `clear()`	**Removes all the elements from the set**
* `copy()`	Returns a copy of the set
* `difference()`	**Returns a set containing the difference between two or more sets**
* `discard()`	**Remove the specified item**
* `intersection()`	Returns a set, that is the intersection of two other sets
* `pop()`	Removes an element from the set
* `remove()`	**Removes the specified element**
* `union()`	Return a set containing the union of sets
* `update()`	**Update the set with the union of this set and others**


```
AC_list1 = {'Origin', 'Unity', 'Syndicate', 'Chronicles', 'Rogue'}
AC_list2 = {'Origin', 'IV Black Flag', 'Syndicate', 'Odyssey', 'Rogue'}

print('intersection same : ', AC_list1.intersection(AC_list2))
# intersection same :  {'Origin', 'Syndicate', 'Rogue'}

print('difference : ', AC_list1.difference(AC_list2))
print('difference : ', AC_list2.difference(AC_list1))


print(AC_list1.union(AC_list2))


# difference :  {'Unity', 'Chronicles'}
# difference :  {'Odyssey', 'IV Black Flag'}
{'Rogue', 'Origin', 'IV Black Flag', 'Odyssey', 'Unity', 'Chronicles', 'Syndicate'}
```

## **6、dict**

```
AS_dict_old = {'1': "Assassin's Creed", '2':["Assassin's Creed II", 'Brotherhood', 'Revelations'], "3":"Assassin's Creed III"}
print('dict:', AS_dict_old)
print('dict:', AS_dict_old['2'])

# output
dict: {'1': "Assassin's Creed", '2': ["Assassin's Creed II", 'Brotherhood', 'Revelations'], '3': "Assassin's Creed III"}
dict: ["Assassin's Creed II", 'Brotherhood', 'Revelations']
```

```
AS_dict_old['4'] = 'IV Black Flag'
print('get:', AS_dict_old.get('3'))
print('get:', AS_dict_old.get('4'))

# get: Assassin's Creed III
# get: IV Black Flag

print('dict:', AS_dict_old)
AS_dict_old.update({'4':"Assassin's Creed IV Black Flag"})
print('dict after update:', AS_dict_old)
# dict after update: {'1': "Assassin's Creed", '2': ["Assassin's Creed II", 'Brotherhood', 'Revelations'], '3': "Assassin's Creed III", '4': "Assassin's Creed IV Black Flag"}

as4 = AS_dict_old.pop('4')
print('dict after pop:', AS_dict_old)
# dict after pop: {'1': "Assassin's Creed", '2': ["Assassin's Creed II", 'Brotherhood', 'Revelations'], '3': "Assassin's Creed III"}

print(as4)
# Assassin's Creed IV Black Flag

print(len(AS_dict_old)) 
# 3

print(AS_dict_old.keys())
# dict_keys(['1', '2', '3'])

print(AS_dict_old.values())
# dict_values(["Assassin's Creed", ["Assassin's Creed II", 'Brotherhood', 'Revelations'], "Assassin's Creed III"])

print(AS_dict_old.items ())
# dict_items([('1', "Assassin's Creed"), ('2', ["Assassin's Creed II", 'Brotherhood', 'Revelations']), ('3', "Assassin's Creed III")])

for key, value in AS_dict_old.items():
	print(key, value)

# 1 Assassin's Creed
# 2 ["Assassin's Creed II", 'Brotherhood', 'Revelations']
# 3 Assassin's Creed III
```


### **6-1 empty**

```
empty_list = []   # empty_list=list()
print(empty_list)

empty_tuple = ()   # empty_tuple=tuple()
print(empty_tuple)

empty_dict = {}   # empty_dict = dict()
print(empty_dict)
```

### **6-2 empty**

```
user= 'Admin'
Password ='Password'
logged_in = True

if user == 'Admin' and Password == 'Password' and logged_in:
    print('welcome to the admin page')
else:
    print('stay at login page')

for ac in AC_list:
    if ac == 'Revolution':
        print('Found, it is supposed to be Unity')
        continue
    print(ac)
```


## **7、Loop**


```
for ac in AC_list:
    for rank in ['good', 'ok', 'bad']:
        print(ac, rank)
```

### **7-1 range**

```
for i in range(1, 11):
	print(i)
# 0...10	
```

```
def main():
    count = 0

    for i in range(0, round(100/7)+1):
        for j in range(0, round(100/3)+1):
            if (100 - i*7 - j*3 >= 0) and (100 - i*7 - j*3) % 2 == 0:
                count += 1
    return count

if __name__ == "__main__":
    print(main())
```


### **7-2 while**

```
x = 0	
while x < 10:
    print(x)
    x += 1
# 0...9
```


## **8、Func**


```
def hello_func():
	return "Assassin's Creed"

print(hello_func().upper())	

def bonjour_func(greeting):
    return f'{greeting} Mademoiselle'

print(bonjour_func('Bonsoir'))
```

## **9、Import function**

```
import random
random_element = random.choice(courses)
print(type(random_element))
for _ in range(10):
	print(random_element)

for _ in range(10):
    print(_)


# import math
# rads = math.radians(90)
# print(rads)
# 1.5707963267948966

import datetime
import calendar
today = datetime.date.today()
print(today)

print(calendar.isleap(2016))
import os 
print(os.getcwd())

import antigravity
```


### **9-1 collections**

```
color = (55, 155, 255)  #tuple
color_dict = {'red':55, 'green': 155, 'blue': 255}  #dictionary

print(color_dict['red']) 
# 55


from collections import namedtuple
Colors = namedtuple('colors', ['red', 'green', 'blue'])
colors = Colors(55, 155, 255)

print(colors[0])
print(colors.green)
print(colors.blue)
# 55
# 155
# 255
```


## **10、Remove duplicates**

### **10-1 Quick remove duplicate from a list**

```
l1 = [1, 2, 3, 1, 2, 3]
l2 = list(set(l1))   
print(l2)

# [1, 2, 3]
```

### **10-2 Quick remove duplicate from a dictionay**


```
result = {}

for key,value in raw_results.items():
        if value not in results.values():
            results[key] = value  
```



## **11、Add elements**

### **11-1 add element to set(): `set.add()`**  

```
nums = [1,2,3,4,5,6,7,8,9,10]
my_set = set()
for n in nums:
    my_set.add(n)

print(my_set)

# {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

### **11-2 add element to list(): `list.add()`**  

```
my_list = []
for n in nums:
    my_list.append(n)

print(my_list)

# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### **11-2 add element to list(): `list.apend()`**  

```
for n in nums:
  my_list2.append(n*n)

print(my_list2)

# [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

```
my_list3= [n*n for n in nums]
print(my_list3)

# [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

```
my_list2 = []
for n in nums:
    if n%2==0:
        my_list2.append(n)
print(my_list2)

# [2, 4, 6, 8, 10]
```


```
my_list3 = []
for letter in 'abcd':
    for num in range(4):
        my_list3.append((letter,num))

print(my_list3)
# [('a', 0), ('a', 1), ('a', 2), ('a', 3), ('b', 0), ('b', 1), ('b', 2), ('b', 3), ('c', 0), ('c', 1), ('c', 2), ('c', 3), ('d', 0), ('d', 1), ('d', 2), ('d', 3)]

my_list3 = [(letter, num) for letter in 'abcd' for num in range(4)]
# [('Bruce', 'Batman'), ('Clark', 'Superman'), ('Peter', 'Spiderman'), ('Logan', 'Wolverine'), ('Wade', 'Deadpool')]
```

## **12、zip function**

```
names = ['Bruce', 'Clark', 'Peter', 'Logan', 'Wade']
heros = ['Batman', 'Superman', 'Spiderman', 'Wolverine', 'Deadpool']
print(list(zip(names, heros)))


# zip()
# list()


# {'Bruce': 'Batman', 'Clark': 'Superman', 'Peter': 'Spiderman', 'Logan': 'Wolverine', 'Wade': 'Deadpool'}
```

```
my_dict = {}
for name, hero in zip(names, heros):
    my_dict[name] = hero
print(my_dict)

# {'Bruce': 'Batman', 'Clark': 'Superman', 'Peter': 'Spiderman', 'Logan': 'Wolverine', 'Wade': 'Deadpool'}
```

```
def find_index(to_search,target):
    for i,value in enumerate(to_search):
        if value == target:
            break
    else:
        return -1
    return i

my_list = ['K8S', 'AWS', 'Python', 'Chef']

index_location = find_index(my_list,'Python')
print(f'Location of target is index: {index_location}')

index_location1 = find_index(my_list,'Ansible')
print(f'Location of target is index: {index_location1}')

# Location of target is index: 2
# Location of target is index: -1
```


## **13、iter function**

```
nums = [1, 2 ,3]

for i in nums:
	print(i, end=" ")

# 1 2 3 <list_iterator object at 0x106fe93d0>
```

```
i_nums = iter(nums)
print(i_nums)

1
2
3
```

```
def sentense(sentense):
    	for word in sentense.split():
            yield word

g_sentence = sentense('this is a test2')

for word in g_sentence:
	print(word)	
```

```
this
is
a
test2
```


## **14、Map function**

```
def myfunc(a, b):
      return a + b

x = map(myfunc, ('apple', 'banana', 'cherry'), ('orange', 'lemon', 'pineapple'))

print(x)
#convert the map into a list, for readability:
print(list(x))
```

```
<map object at 0x10c5515e0>
['appleorange', 'bananalemon', 'cherrypineapple']
```

