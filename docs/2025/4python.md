## Basic Data types

#### Q.1 What are the basic data types in Python

**Ans: The basic data types in Python are:**

- **Integer**: represents whole numbers.
- **Float**: represents decimal numbers.
- **String**: represents a sequence of characters.
- **Boolean**: represents either True or False.
- **List**: represents an ordered collection of elements.

#### Q.2 How do you convert a string to an integer in Python?

**You can use the int() function** to convert a string to an integer. For example:

```
num_str = "10"
num_int = int(num_str)
```

#### Q.3 How do you check the data type of a variable in Python?

You can use the ``"type ()"`` function to check the data type of a variable. For example:

```
num = 10
print(type(num)) # Output: ‹class 'int'›
```

#### Q.4 What is the difference between a list and a tuple in

**A list is mutable**, **which means you can modify its elements, while a tuple is immutable**, meaning its elements cannot be changed after creation.

#### Q.5 How do you create an empty dictionary in Python?

You can create an empty dictionary using either the **curly braces {}** or the **dict()** function. For

example:

```
empty_dict = {}
empty_dict = dict()
```

## Topic 2 OOPS concept in Python:

#### Q.1 What is OOPS and how is it implemented in Python?

Object-Oriented Programming (OOPS) is a programming paradigm that uses objects to represent real-world entities. 

In Python, OOPS is implemented through classes and objects. Classes are blueprints for creating objects, and objects are instances of a class.

#### Q.2 What are the four principles of OOPS?

Ans: The four principles of OOPS are:

- • Encapsulation: bundling of data and methods that operate on that data within a single unit (class).
- • Inheritance: ability of a class to inherit properties and methods from its parent class.
- • Polymorphism: ability of an object to take on different forms or behaviors based on the context.
- • Abstraction: representing essential features and hiding unnecessary details to simplify the complexity.

以下是 Python 面向对象编程（Object-Oriented Programming）特性的中文翻译：

- **封装（Encapsulation）：将数据及操作数据的方法捆绑在单一单元（类）中**。
- **继承（Inheritance）：子类继承父类属性和方法的能力**。
- **多态（Polymorphism）：对象根据上下文呈现不同形式或行为的能力**。
- **抽象（Abstraction）：通过展现核心特征并隐藏非必要细节来简化复杂性**。

**封装： 强调数据与方法的整合，通过访问控制（如私有变量 `_var`）实现信息隐藏**

**继承: 子类复用父类逻辑（`class Child(Parent)`），支持多层继承和方法重写**

**多态**

- 方法重写（子类覆写父类方法）
- 鸭子类型（不同对象实现相同方法名）

```
class Dog:
    def speak(self): print("汪汪")
    
class Cat:
    def speak(self): print("喵喵")
    
def animal_sound(obj):  # 多态调用
    obj.speak()
```

**抽象**

通过抽象基类（ABC）或接口设计，聚焦核心功能：

```
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self):  # 强制子类实现核心逻辑
        pass
```

#### Q.5 What is the difference between a class method and an instance method in Python?

Ans: A class method is a method bound to the class and not the instance of the class. 

It is defined using the @classmethod decorator and can access only class-level variables. 

On the other hand, an instance method is bound to the instance of the class and can access both instance and class-level


## Topic 3 String handling functions

#### Q.1 How do you concatenate two strings in Python?

You can concatenate two strings using the + operator. For example:

```
str1 = "Hello"
str2 = "World"
result = stri + str2 # Output: "HelloWorld":
```

#### Q.2 How do you find the length of a string in Python?

You can use the len() function to find the length of a string. For example:

```
str1 = "Hello World"
length = len(str1) # Output:
```

#### Q.3 How do you convert a string to uppercase in Python?


Ans: You can use the upper) method to convert a string to uppercase. For example:

```
str1 = "hello"
uppercase_str = str1. upper) # Output: "HELLO"
```

#### Q.4 How do you split a string into a list of substrings in Python?

You can use the `split（)` method to split a string into a list of substrings based on a delimiter. For example:

```
str1 = "Hello,World"
substrings = str1.split(",")

# Output: ["Hello",World"]
```

#### Q.5 How do you check if a string contains a specific substring in Python?

You can use the in keyword to check if a substring is present in a string. For example:

```
str1 = "Hello World"
is_present = "World" in str1 # Output: True
```

## Topic 4 Control statements, functions in Python:

```
if condition:
# Code block executed if the condition is

True else:
# Code block executed if the condition is
False
```

#### Q.3 How do you define a function in Python?

A function in Python is defined using the def keyword. For example:

```
def greet():
	print("Hello, world!")
```

### Q.4 How do you pass arguments to a function in Python?

```
def greet (name):
	print("Hello," + name + "!")
```

#### Q.5 How do you return a value from a function in Python?

```
def add (a, b):
 return a + b
```

## Topic 5 Special data types in Python:

**Q.1 What is a set in Python?**

A set in Python is an unordered collection of unique elements.

```
my_set =  {1,2,3} #Output: {1,2,3}
```

Q.2 What is a dictionary in Python?

```
my_dict = {"name": "John", "age": 25}
```


**Q.3 How do you access values in a dictionary in Python?**

You can access values in a dictionary by using the corresponding key. For example:

```
my_dict = {"name": "John", "age": 25}
print(my_dict["name"]) # Output: "John"
```

**Q.4 What is a tuple in Python?**

A tuple in Python is an ordered and immutable collection of elements. It is defined using parentheses `() or the tuple()` constructor. For example:

```
my_tuple = (1, 2, 3) # Output: (1, 2, 3)
```

**Q.5 How do you swap the values of two variables in Python?**

```
a = 5
b = 10
a, b = b, a
print(a, b) # Output: 10, 5
```

## 3 Lambda functions， list comprehension:

#### Q.1 What is a lambda function in Python?

A lambda function is an anonymous function defined using the lambda keyword. It is typically used for short, one-line functions. For example:

```
square = lambda x: x**2
print (square (3)) # Output: 9
```

#### Q.2 What is list comprehension in Python?

List comprehension is a concise way to create lists in Python based on existing lists or other iterables. It combines the creation of a new list with a loop and optional conditional statements. For example:

```
numbers = [1, 2, 3, 5]
squared_numbers = [x**2 for x in numbers]
print(squared_numbers)

# Output: [1, 4, 9, 16, 25]
```

#### Q.3 How do you filter elements in a list using list

You can filter elements in a list using list comprehension by adding a conditional statement.

For example, to filter even numbers:

```
numbers = [1, 2, 3,4, 5]
even_numbers = Ix for x in numbers if x % 2 == 0]
print (even_numbers) # Output: [2, 4]
```

#### Q.4 Can you have multiple if conditions in list comprehension?

Yes, you can have multiple if conditions in list comprehension by chaining them using the and or or operators. 

```
numbers = [1, 2, 3, 4, 5]
filtered_numbers = [x for x in numbers if x % 2==0 and x > 2]
print(filtered_numbers) 

# Output: [4]
```

#### Q.5 How do you create a dictionary using list comprehension in Python?

Ans: You can create a dictionary using list comprehension by specifying key-value pairs within curly braces `{}`.

For example:

```
keys = ['a', 'b', 'c']
values = [1, 2, 3]
my_dict = {k: v for k, v in zip(keys, values)}
print(my_dict) 

# Output: {'a': 1, 'b': 2, 'c': 3}
```