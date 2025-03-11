# 12 Python Interview - Full

## 1 python基础命令

### 打印输出：`print()`

```
# 输出简单的欢迎信息
print("欢迎来到Python编程世界！")
```

输出变量值：

```
name = "Alice"
# 输出变量的值，使用逗号分隔不同的参数
print("你好,", name)
```

使用f-string格式化输出：

```
age = 25
# 使用f-string嵌入变量到字符串中
print(f"我今年{age}岁了。")
```

输出多个变量，用空格分隔：

```
first_name = "张"
last_name = "三"
# 输出多个变量，自动以空格分隔
print(first_name, last_name)
```

控制输出不换行：

```
# 使用end参数控制输出后不换行，默认是换行符'\n'
print("这是第一行", end=" ")
print("这是在同一行继续输出的内容")
```

### 变量定义与赋值

定义并赋值一个整数变量：

```
# 定义一个整数类型的变量
my_age = 28
print(my_age)  # 输出变量的值
```

## 2 如何在Python中使用高级数据结构？

### **1. collections 模块**

collections 模块提供了一些专门的数据结构，比如：

**Counter: 用于计数可哈希对象。**

```
from collections import Counter
li = ["Dog", "Cat", "Mouse", 42, "Dog", 42, "Cat", "Dog"]
a = Counter(li)
print(a)  # 输出: Counter({'Dog': 3, 42: 2, 'Cat': 2, 'Mouse': 1})
```

**defaultdict: 类似于普通的字典，但在访问不存在的键时会自动创建一个默认值**。

```
from collections import defaultdict
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]

for k, v in s:
    d[k].append(v)

print(d)  # 输出: defaultdict(, {'yellow': [1, 3], 'blue': [2, 4], 'red': [1]})
```

deque: 双端队列，支持从两端快速添加或移除元素。

```
from collections import deque
d = deque("ghi")
d.append('j')
d.appendleft('f')
print(d)  # 输出: deque(['f', 'g', 'h', 'i', 'j'])
```

###  2. heapq 模块

heapq 提供了堆队列算法的实现，**也称为优先队列算法**。

**最小堆：每个父节点的值都小于或等于其子节点的值。**

```
import heapq
heap = []
heapq.heappush(heap, (5, 'write code'))
heapq.heappush(heap, (7, 'release product'))
heapq.heappush(heap, (1, 'write spec'))
print(heapq.heappop(heap))  # 输出: (1, 'write spec')
```

### 3. bisect 模块

**bisect 提供了对已排序列表的支持，允许你进行二分查找和插入操作。**

二分查找：

```
import bisect
sorted_list = [1, 2, 4, 5]
position = bisect.bisect(sorted_list, 3)
print(position)  # 输出: 2
```

### 4. array 模块

**array 提供了一种数组类型，它比列表更加紧凑，但是只能存储相同类型的数值。**

创建数组：

```
import array
arr = array.array('i', [1, 2, 3, 4])
print(arr)  # 输出: array('i', [1, 2, 3, 4])
```

## 3 python 30个函数的高级用法

### **1 使用默认参数值**

```
def greet(name, message="你好"):
    # 打印问候信息，默认消息为“你好”
    print(f"{name}, {message}")
greet("张三")  # 输出: 张三, 你好
```

### **2 关键字参数调用函数**

```
def describe_pet(animal_type, pet_name):
    # 描述宠物的信息
    print(f"\n我有一个{animal_type}。")
    print(f"它的名字叫{pet_name.title()}。")
describe_pet(pet_name='哈奇', animal_type='狗')  # 指定参数名传参
```

### **3 收集任意数量的位置参数**

```
收集任意数量的位置参数

def make_pizza(*toppings):
    # 打印所点披萨的所有配料
    print(toppings)
make_pizza('pepperoni')
make_pizza('mushrooms', 'green peppers', 'extra cheese')
```

### **4 收集关键字参数**

```
def build_profile(first, last, **user_info):
    # 创建一个字典，包含用户信息
    profile = {'first_name': first, 'last_name': last}
    profile.update(user_info)
    return profile
print(build_profile('albert', 'einstein', location='princeton', field='physics'))
```

### **5 lambda 表达式**

```
add = lambda x, y: x + y  # 定义一个简单的lambda函数用于加法
print(add(5, 3))  # 输出：8
```

### **6 函数作为另一个函数的参数**

```

函数作为另一个函数的参数

def operation(x, y, func):
    # 调用传递进来的函数
    return func(x, y)
print(operation(10, 5, lambda a, b: a - b))  # 输出：5
```


### **7 使用装饰器**

```
def decorator_function(original_function):
    # 定义一个简单的装饰器，添加额外功能
    def wrapper_function():
        print('在调用之前执行一些操作')
        return original_function()
    return wrapper_function
@decorator_function
def display():
    print('display function ran')
display()  # 首先输出装饰器内的打印，然后是display函数的内容

# 在调用之前执行一些操作
# display function ran
```

### **8 带参数的装饰器**

```
def prefix_decorator(prefix):
    def decorator(original_function):
        def new_function():
            print(f"{prefix} 在原始函数前执行")
            return original_function()
        return new_function
    return decorator
@prefix_decorator("LOG:")
def say_hello():
    print("Hello!")
say_hello()

# LOG: 在原始函数前执行
# Hello!
```

### **9 闭包**

```
def outer_function(msg):
    def inner_function():
        print(msg)  # 内部函数访问外部函数的变量
    return inner_function
hi_func = outer_function('Hi!')
bye_func = outer_function('Bye!')
hi_func()
bye_func()
```

```
Hi!
Bye!
```

### **10 使用 global 关键字**

```
global_var = 0
def update_global():
    global global_var  # 声明使用全局变量
    global_var += 1
update_global()
print(global_var)  # 输出：1
```
### **11 使用 nonlocal 关键字**

```
def outer():
    x = "local"
    def inner():
        nonlocal x  # 声明使用外部函数变量
        x = 'nonlocal'
        print("inner:", x)
    inner()
    print("outer:", x)
outer()  # 输出: inner: nonlocal \n outer: nonlocal
```

```
inner: nonlocal
outer: nonlocal
```

### **12 函数返回值为另一个函数**


```
def make_multiplier_of(n):
    # 返回一个函数，该函数将传入的参数乘以 n
    def multiplier(x):
        return x * n
    return multiplier
times3 = make_multiplier_of(3)
print(times3(9))  # 输出：27
```


### **13 匿名函数与 sorted 结合使用**

```
pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
sorted_pairs = sorted(pairs, key=lambda pair: pair[1])  # 按第二个元素排序
print(sorted_pairs)  # 输出：[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

```
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

### **14 使用 `functools.partial` 固定函数参数**

```
from functools import partial
def multiply(a, b):
    return a * b
double = partial(multiply, 2)
print(double(5))  # 输出：10
```

### **15 使用 inspect 获取函数签名**

```
import inspect
def foo(a, b=2, *args, **kwargs):
    pass
sig = inspect.signature(foo)
print(sig)  # 输出：(a, b=2, *args, **kwargs)
```

### **16 使用 map 应用函数到列表**

```
numbers = [1, 2, 3, 4]
squared = list(map(lambda x: x**2, numbers))
print(squared)  # 输出：[1, 4, 9, 16]
```

### **17 使用 filter 筛选列表**

```
even_numbers = list(filter(lambda x: x % 2 == 0, range(10)))
print(even_numbers)  # 输出：[0, 2, 4, 6, 8]
```

### **18 使用 reduce 进行累积操作**

```
def show_locals():
    x = 1
    y = 2
    print(locals())  # 输出：{'x': 1, 'y': 2}
show_locals()
```

### **19 使用 exec 动态执行代码**

```
code = "a = 5\nb=6\nprint('a + b =', a + b)"
exec(code)  # 输出：a + b = 11
```

### **20 使用函数作为参数的高阶函数**

```
def apply_function(func, value):
    # 接受一个函数和一个值，然后应用该函数到这个值上
    return func(value)
print(apply_function(lambda x: x**2, 4))  # 输出：16
```

### **21  递归函数计算阶乘**

```
def factorial(n):
    if n == 0:
        return 1
    else:
        return n * factorial(n-1)
print(factorial(5))  # 输出：120
```


### **22  使用列表推导式与函数结合**

```
def square(x):
    return x*x
numbers = [1, 2, 3, 4]
squares = [square(num) for num in numbers]
print(squares)  # 输出：[1, 4, 9, 16]
```

### **23 带有文档字符串的函数**

```
def doc_example():
    """
    这是一个带有文档字符串的例子。
    它描述了函数的功能、参数和返回值。
    """
    pass
print(doc_example.__doc__)  # 输出函数的文档字符串
```

### **24  使用` *args` 和 `**kwargs` 同时传递参数**

```
def arg_kwarg_example(*args, **kwargs):
    print("Args:", args)
    print("Kwargs:", kwargs)
arg_kwarg_example(1, 2, a=3, b=4)
# 输出：
# Args: (1, 2)
# Kwargs: {'a': 3, 'b': 4}
```

### **25  使用 type hints 提供类型提示**

**使用 type hints 提供类型提示**

```
def greeting(name: str) -> str:
    return 'Hello, ' + name
print(greeting('world'))  # 输出：Hello, world
```

### **26 使用 wraps 装饰器复制原始函数元数据**

```
from functools import wraps
def my_decorator(f):
    @wraps(f)
    def wrapper(*args, **kwargs):
        print("Calling function.")
        return f(*args, **kwargs)
    return wrapper
@my_decorator
def example():
    """Docstring"""
    print("Inside example function")
print(example.__name__)  # 输出：example
print(example.__doc__)   # 输出：Docstring
```

### **27 使用 contextlib.contextmanager 创建上下文管理器**

```
from functools import wraps
def my_decorator(f):
    @wraps(f)
    def wrapper(*args, **kwargs):
        print("Calling function.")
        return f(*args, **kwargs)
    return wrapper
@my_decorator
def example():
    """Docstring"""
    print("Inside example function")
print(example.__name__)  # 输出：example
print(example.__doc__)   # 输出：Docstring
```

### **28 使用 contextlib.contextmanager 创建上下文管理器**

```
from contextlib import contextmanager
@contextmanager
def tag(name):
    print(f"<{name}>")
    yield
    print(f"")
with tag("h1"):
    print("标题文本")
```

### **29 动态创建函数**

动态创建函数

```
def create_multiplier(n):
    def multiplier(x):
        return x * n
    return multiplier
double = create_multiplier(2)
triple = create_multiplier(3)
print(double(4), triple(4))  # 输出：8 12
```

### **30 使用 operator 模块中的函数代替 lambda 表达式**

```
import operator
numbers = [1, 2, 3, 4]
sum_result = reduce(operator.add, numbers)
print(sum_result)  # 输出：10
```


## 4 深度解析python os 模块、sys 模块、path 模块

### 1 OS 模块

os模块提供了与操作系统交互的功能。可以用来读取或设置环境变量、管理文件目录等。

获取当前工作目录

```
import os
# 获取当前的工作目录
current_directory = os.getcwd()
print("当前工作目录:", current_directory)
```
```
当前工作目录: /Users/i515190/k8s_test/python/2025
```

创建目录

```
# 创建一个新目录
os.mkdir('新建文件夹')
print("已创建目录")
```

列出目录内容

```
# 列出指定目录的内容
files = os.listdir('.')
print("当前目录下的文件和文件夹:", files)
```
```
当前目录下的文件和文件夹: ['test.py']
```

**删除空目录**

```
# 删除之前创建的目录
os.rmdir('新建文件夹')
print("目录已删除")
```

**执行系统命令**

```
# 执行系统命令并获取结果
result = os.system('echo Hello World')
print("执行系统命令的结果:", result)
```

### 2 sys 模块

sys模块提供了对解释器使用或维护的一些变量和函数的访问。

打印Python解释器版本

```
import sys
# 打印Python解释器的版本信息
print("Python版本:", sys.version)
```

```
Python版本: 3.9.0 (default, Oct  5 2023, 20:12:44) 
[Clang 14.0.0 (clang-1400.0.29.202)]
```

退出程序

```
# 退出Python程序
sys.exit("程序正常退出")
```

**获取命令行参数**

```
# 打印从命令行传递给脚本的参数
print("命令行参数:", sys.argv)
```

命令行参数: ['test.py']

**标准输入输出重定向**

```
# 将输出重定向到一个文件
temp = sys.stdout
sys.stdout = open('log.txt', 'w')
print("这将被写入log.txt")
sys.stdout.close()
sys.stdout = temp
print("标准输出已恢复")
```

**获取最大整数**

```
# Python中可处理的最大整数值
print("Python支持的最大整数:", sys.maxsize)
```

### 3 os.path 模块

**os.path子模块提供了用于操作文件路径的工具。**

连接路径

```
# 使用os.path.join()安全地组合路径
path = os.path.join('用户', '文档', '文件.txt')
print("组合后的路径:", path)
```

检查路径是否存在

```
# 检查某个路径是否存在
exists = os.path.exists('.')
print("路径存在吗:", exists)
```

判断是否为文件

```
# 检查路径是否指向一个文件
is_file = os.path.isfile('test.py')  # 假设'test.py'是存在的
print("这是一个文件吗:", is_file)
```

```
这是一个文件吗: True
```

获取文件大小

```
# 获取文件的大小（以字节为单位）
size = os.path.getsize('test.py')  # 假设'test.py'是存在的
print("文件大小:", size, "字节")
```

```
文件大小: 2816 字节
```

获取文件的绝对路径

```
# 获取文件的绝对路径
abs_path = os.path.abspath('test.py')  # 假设'test.py'是存在的
print("文件的绝对路径:", abs_path)
```
```
文件的绝对路径: /Users/i515190/k8s_test/python/2025/test.py
```










