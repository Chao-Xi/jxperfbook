# **GS interview preparation**

## **Char**


### **Non Repeating Character**

```
# Given a string S consisting of lowercase Latin Letters. Find the first non-repeating character in S.

# Input:
# S = hello
# Output: h
# Explanation: In the given string, the
# first character which is non-repeating
# is h, as it appears first and there is
# no other 'h' in the string.

# Input:
# S = zxvczbtxyzvy
# Output: c
# Explanation: In the given string, 'c' is
# the character which is non-repeating. 
```

```
def main(str):
    char_order = []
    counts = {}
    for c in str:
        if c in counts:
            counts[c]+=1
        else:
            counts[c] = 1 
            char_order.append(c)
    
    for c in char_order:
        if counts[c] == 1:
            return c
        
    return None
```

```
if  __name__ == "__main__":
    print(main("PythonforallPythonMustforall"))
    print(main('tutorialspointfordeveloper'))
    print(main('AABBCC'))
```


```
$ python 8onorepc.py 
M
u
None
```




## **String** 

### **1 Reverse words in a given string**

```
# Given a String S, reverse the string without reversing its individual words. 
# Words are separated by dots.

# Input:
# S = i.like.this.program.very.much
# Output: much.very.program.this.like.i
# Explanation: After reversing the whole
# string(not individual words), the input
# string becomes
# much.very.program.this.like.i

# Input:
# S = pqr.mno
# Output: mno.pqr
# Explanation: After reversing the whole
# string , the input string becomes
# mno.pqr
```

```
def main(Str):
    s = Str.split(".");
    for i in range(len(s)-1,-1,-1):
        print(s[i],end=".")

if __name__ == "__main__":
    main("i.like.this.program.very.much")
```

**Output**

```
much.very.program.this.like.i.
```


### **Find the first repeated word in a string**

Given a string, Find the 1st repeated word in a string

Examples: 

```
Input : "Ravi had been saying that he had been there"
Output : had

Input : "Ravi had been saying that"
Output : No Repetition

Input : "he had had he"
Output : he
```

```
def firstRepeatString(str):
    char_list = []
    counts = {}
    count = 0
    raw_list = str.split(' ')
    for i in range(len(raw_list)):
        if raw_list[i] in counts:
            counts[raw_list[i]] += 1
        else:
            counts[raw_list[i]] = 1
            char_list.append(raw_list[i])
    
    
    for c in char_list:
        if counts[c] > 1:
            print(c)
            break
    
firstRepeatString("he had had he")
```

```
from collections import Counter 
# Python program to find the first
# repeated character in a string
def firstRepeatedWord(sentence):
 
    # splitting the string
    lis = list(sentence.split(" "))
     
    # Calculating frequency of every word
    frequency = Counter(lis)
     
    # Traversing the list of words
    for i in lis:
       
        # checking if frequency is greater than 1
         
        if(frequency[i] > 1):
            # return the word
            return i
 
# Driver code
sentence = "Vikram had been saying that he had been there"
print(firstRepeatedWord(sentence))
```

```
$ python3 21repreatstring.py 
had
```

### **最长公共前缀**

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

```
示例 1:

输入: ["flower","flow","flight"]
输出: "fl"
示例 2:

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。
```

**Option1**

```
示例 1:

输入: ["flower","flow","flight"]
输出: "fl"
示例 2:

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。
```

```
def longest_string(arr):
    result = ""
    for i in zip(*arr):
        if len(set(i)) == 1:
            result += i[0]
        else:
            break
    return result

arr = ["a","ab","abc"]
print(longest_string(arr))
```

**Option2**

```
def longest_str(str):
    if not str:
        return ""
    res = str[0]
    i = 1
    while i < len(str):
        while str[i].find(res) != 0:
            res = res[0:len(res)-1]
        i += 1
    return res

print(longest_str(["aabc","ab"]))
```


## **Array** 

### **Two Sum**

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

**Option1**

```
def twoSum(nums, target):
    """
    :type nums: List[int]
    :type target: int
    :rtype: List[int]
    """
    idxDict = dict()
    for idx, num in enumerate(nums):
        if target - num in idxDict:
            return [idxDict[target - num], idx]
        idxDict[num] = idx


nums = [2, 7, 11, 15]
target = 9

print(twoSum(nums,target))
```

**Option2**

```
def twoSumNaive(num_arr, pair_sum):
    # search first element in the array
    for i in range(len(num_arr)-1):
        # search other element in the array
        for j in range(i + 1, len(num_arr)):
        # if these two elemets sum to pair_sum, print the pair
            if num_arr[i] + num_arr[j] == pair_sum:
                print("Pair with sum", pair_sum,"is: (", num_arr[i],",",num_arr[j],")")
                print([i,j])

# Driver Code
num_arr = [3, 5, 2, -4, 8, 11]
pair_sum = 7

# Function call inside print
twoSumNaive(num_arr, pair_sum) 

# Pair with sum 7 is: ( 5 , 2 )
# [1, 2]
# Pair with sum 7 is: ( -4 , 11 )
# [3, 5]
```

**Option3**

```
def twoSumHashing(num_arr, pair_sum):
    sums = []
    hashTable = {}

    for i in range(len(num_arr)):
        complement = pair_sum - num_arr[i]
        if complement in hashTable:
            print("Pair with sum", pair_sum,"is: (", num_arr[i],",",complement,")")
            # print([i,hashTable[pair_sum - num_arr[i]]])
        hashTable[num_arr[i]] = num_arr[i]

# Driver Code
num_arr = [4, 5, 1, 8]
pair_sum = 9

# Calling function
twoSumHashing(num_arr, pair_sum)
```


### **Check if two arrays are equal or not**

```
# Check if two arrays are equal or not
# Given two given arrays of equal length, the task is to find if given arrays are equal or not. 
# Two arrays are said to be equal if both of them contain the same set of elements, 
# arrangements (or permutation) of elements may be different though.

# Input  : arr1[] = {1, 2, 5, 4, 0};
#          arr2[] = {2, 4, 5, 0, 1}; 
# Output : Yes

# Input  : arr1[] = {1, 2, 5, 4, 0, 2, 1};
#          arr2[] = {2, 4, 5, 0, 1, 1, 2}; 
# Output : Yes
 
# Input : arr1[] = {1, 7, 1};
#         arr2[] = {7, 7, 1};
# Output : No
```

```
def areEqual(arr1, arr2, n, m):
     
    # If lengths of array are not
    # equal means array are not equal
    if (n != m):
        return False
 
    # Sort both arrays
    arr1.sort()
    arr2.sort()
 
    # Linearly compare elements
    for i in range(0, n - 1):
        if (arr1[i] != arr2[i]):
            return False
 
    # If all elements were same.
    return True
 
 
# Driver Code
arr1 = [3, 5, 2, 5, 2]
arr2 = [2, 3, 5, 5, 2]
n = len(arr1)
m = len(arr2)
 
if (areEqual(arr1, arr2, n, m)):
    print("Yes")
else:
    print("No")
```

### **Sort an array in wave form**

```
# Given an unsorted array of integers, 
# sort the array into a wave like array. 
# An array ‘arr[0..n-1]’ is sorted in wave form if arr[0] >= arr[1] <= arr[2] >= arr[3] <= arr[4] >= …..

#  Input:  arr[] = {10, 5, 6, 3, 2, 20, 100, 80}
#  Output: arr[] = {10, 5, 6, 2, 20, 3, 100, 80} OR
#                  {20, 5, 10, 2, 80, 6, 100, 3} OR
#                  any other array that is in wave form

#  Input:  arr[] = {20, 10, 8, 6, 4, 2}
#  Output: arr[] = {20, 8, 10, 4, 6, 2} OR
#                  {10, 8, 20, 2, 6, 4} OR
#                  any other array that is in wave form

#  Input:  arr[] = {2, 4, 6, 8, 10, 20}
#  Output: arr[] = {4, 2, 8, 6, 20, 10} OR
#                  any other array that is in wave form

#  Input:  arr[] = {3, 6, 5, 10, 7, 20}
#  Output: arr[] = {6, 3, 10, 5, 20, 7} OR
#                  any other array that is in wave form
```

```
# Python function to sort the array arr[0..n-1] in wave form,
# i.e., arr[0] >= arr[1] <= arr[2] >= arr[3] <= arr[4] >= arr[5]
def sortInWave(arr, n):
     
    #sort the array
    arr.sort()

    # Swap adjacent elements
    for i in range(0,n-1,2):
        arr[i], arr[i+1] = arr[i+1], arr[i]
    
    # Driver program
arr = [10, 90, 49, 2, 1, 5, 23]
sortInWave(arr, len(arr))
for i in range(0,len(arr)):
    print (arr[i],end=" ")
```

### **Stock buy and sell**

```
# The cost of stock on each day is given in an array A[] of size N. 
# Find all the days on which you buy and sell the stock so that in between those days your profit is maximum.
# Note: There may be multiple possible solutions. 
# Return any one of them. Any correct solution will result in an output of 1, 
# whereas wrong solutions will result in an output of 0.

# Input:
# N = 7
# A[] = {100,180,260,310,40,535,695}
# Output:
# 1
# Explanation:
# One possible solution is (0 3) (4 6)
# We can buy stock on day 0,
# and sell it on 3rd day, which will 
# give us maximum profit. Now, we buy 
# stock on day 4 and sell it on day 6.

# Input:
# N = 5
# A[] = {4,2,2,2,4}
# Output:
# 1
# Explanation:
# There are multiple possible solutions.
# one of them is (3 4)
# We can buy stock on day 3,
# and sell it on 4th day, which will 
# give us maximum profit.
```

```
def maxProfit(pricelist):
    max_profit = 0
    result = []
    for i in range(len(pricelist)-1):
        for j in range(i+1, len(pricelist)):
            profit = pricelist[j] - pricelist[i]
            if profit > max_profit:
                max_profit = profit
    return max_profit
    
print(maxProfit([100,180,260,310,40,535,695]))
```
```
655
```

```
class Solution:
    def maxProfit(self, priceslist):
        min_price = float("inf")
        max_profit = 0
        for i in range(len(priceslist)):
            if priceslist[i] < min_price:
                min_price = priceslist[i]
            elif priceslist[i] - min_price > max_profit:
                max_profit = priceslist[i] - min_price
        return max_profit

s = Solution();


print(s.maxProfit([100,180,260,310,40,535,695]))
```

* `float("inf")` used for setting a variable with an infinitely large value.


### 在数组 `{ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 }` 中，查找 8 是否出现过。

```
def main():
# 需要查找的数字
    targetNumb = 8
    arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    middle = 0
    low = 0
    high = len(arr) - 1
    isfind = 0

    while low <= high:
        middle = round((low + high) /2)
        if arr[middle] ==  targetNumb:
            print(f"{targetNumb} in arr, and the index is {middle}")
            isfind = 1
            break
        elif arr[middle] > targetNumb:  #  说明该数在low~middle之间
            high = middle - 1
        else:                       #  说明该数在middle~high之间
            low = middle + 1
    if isfind == 0:
        print(f"{targetNumb} is not in the arr")


if __name__ == "__main__":
    main()

# 二分查找的时间复杂度是 O(logn)，
```




## **Sort** 

### **Bubble Sort**

```
# 冒泡排序最好时间复杂度是 O(n)
# 冒泡排序最坏时间复杂度会比较惨，是 O(n*n)。

# 从第一个数据开始，依次比较相邻元素的大小。如果前者大于后者，则进行交换操作，把大的元素往后交换。通过多轮迭代，直到没有交换操作为止

def main():
    arr = [ 1, 0, 3, 4, 5, -6, 7, 8, 9, 10 ]
    print(f"The the original data order: {arr}")
    for i in range(1,len(arr)):           # Start from 2nd one
        for j in range(0, len(arr)-i):    # Start from 1st one
           if arr[j] > arr[j+1]:
               temp = arr[j]
               arr[j] = arr[j + 1]
               arr[j + 1] = temp
    
    print(f"The the Bubble sort data order: {arr}")

if __name__ == "__main__":
    main()
```

### **Insert Sort**

```
# 1、插入排序的原理
# 选取未排序的元素，插入到已排序区间的合适位置，直到未排序区间为空
# 插入排序的平均时间复杂度是 O(n*n)。

def main():
    arr = [ 2, 3, 5, 1, 23, 6, 78, 34 ]
    print(f"The the original data order: {arr}")
    for i in range(1,len(arr)):
        temp = arr[i]
        j = i - 1
        for j in range(j,0-1):
            if  arr[j] > temp:
                arr[j+1] = arr[j]
            else:
                break
        arr[j+1] = temp

    print(f"The the sorted data order: {arr}")

if __name__ == "__main__":
    main()
```

### **Merge Sort**

```
import sys

def merge_sort(A):
	merge_sort2(A, 0, len(A)-1)

def merge_sort2(A, first, last):
#  In Python 3, they made the / operator do a floating-point division, 
#  and added the // operator to do integer division 
	if first < last:
		middle = (first + last)//2
		merge_sort2(A, first, middle)
		merge_sort2(A, middle+1, last)
		merge(A, first, middle, last)

def merge(A, first, middle, last):
	L = A[first:middle+1]
	R = A[middle+1:last+1]
	L.append(sys.maxsize)  #Reach the end of list
	R.append(sys.maxsize)
	i = j = 0
	#Index i for left half and j for the right half
	#Initialize was 0

	for k in range (first, last+1):
		if L[i] <= R[j]:
			A[k] = L[i]
			i += 1
		else:
			A[k] = R[j]
			j += 1

A = [5,9,1,2,4,8,6,3,7]
print(A)
merge_sort(A)
print(A)
```

### **Quick Sort**

```
# This Function handles sorting part of quick sort
# start and end points to first and last element of
# an array respectively
def partition(start, end, array):
     
    # Initializing pivot's index to start
    pivot_index = start
    pivot = array[pivot_index]
     
    # This loop runs till start pointer crosses
    # end pointer, and when it does we swap the
    # pivot with element on end pointer
    while start < end:
         
        # Increment the start pointer till it finds an
        # element greater than  pivot
        while start < len(array) and array[start] <= pivot:
            start += 1
             
        # Decrement the end pointer till it finds an
        # element less than pivot
        while array[end] > pivot:
            end -= 1
         
        # If start and end have not crossed each other,
        # swap the numbers on start and end
        if(start < end):
            array[start], array[end] = array[end], array[start]
     
    # Swap pivot element with element on end pointer.
    # This puts pivot on its correct sorted place.
    array[end], array[pivot_index] = array[pivot_index], array[end]
    
    # Returning end pointer to divide the array into 2
    return end
     
# The main function that implements QuickSort
def quick_sort(start, end, array):
     
    if (start < end):
         
        # p is partitioning index, array[p]
        # is at right place
        p = partition(start, end, array)
         
        # Sort elements before partition
        # and after partition
        quick_sort(start, p - 1, array)
        quick_sort(p + 1, end, array)
         
# Driver code
array = [ 10, 7, 8, 9, 1, 5 ]
quick_sort(0, len(array) - 1, array)
 
print(f'Sorted array: {array}')
```

## **Tree**

### **Sum Tree**

```
# Given a Binary Tree. Return true if, for every node X in the tree other than the leaves, 
# its value is equal to the sum of its left subtree's value and its right subtree's value. Else return false.

# An empty tree is also a Sum Tree as the sum of an empty tree can be considered to be 0. 
# A leaf node is also considered a Sum Tree.

# Input:
#     3
#   /   \    
#  1     2

# Output: 1
# Explanation:
# The sum of left subtree and right subtree is
# 1 + 2 = 3, which is the value of the root node.
# Therefore,the given binary tree is a sum tree.
```

```
# A class to store a binary tree node
class Node:
    def __init__(self, key=None, left=None, right=None):
        self.key = key
        self.left = left
        self.right = right
 
 
# Recursive function to check if a given binary tree is a sum tree or not
def isSumTree(root):
 
    # base case: empty tree
    if root is None:
        return 0
 
    # special case: leaf node
    if root.left is None and root.right is None:
        return root.key
 
    left = isSumTree(root.left)
    right = isSumTree(root.right)
 
    # if the root's value is equal to the sum of all elements present in its
    # left and right subtree
    if left != -sys.maxsize and right != -sys.maxsize and root.key == left + right:
        return 2 * root.key
 
    return -sys.maxsize
 
 
if __name__ == '__main__':

#   Construct the following tree
#              44
#             /  \
#            /    \
#           9     13
#          / \    / \
#         4   5  6   7
    root = Node(44)
    root.left = Node(9)
    root.right = Node(13)
    root.left.left = Node(4)
    root.left.right = Node(5)
    root.right.left = Node(6)
    root.right.right = Node(7)

    if isSumTree(root) != -sys.maxsize:
        print('Binary tree is a sum tree')
    else:
        print('Binary tree is not a sum tree')
```


### **Binary Tree to DLL**

```
# Given a Binary Tree (BT), convert it to a Doubly Linked List(DLL) In-Place. 
# The left and right pointers in nodes are to be used as previous and next pointers respectively in converted DLL. 
# The order of nodes in DLL must be same as Inorder of the given Binary Tree. 
# The first node of Inorder traversal (leftmost node in BT) must be the head node of the DLL.

# Input:
#       1
#     /  \
#    3    2
# Output:
# 3 1 2 
# 2 1 3 
# Explanation: DLL would be 3<=>1<=>2

# Input:
#        10
#       /   \
#      20   30
#    /   \
#   40   60
# Output:
# 40 20 60 10 30 
# 30 10 60 20 40
# Explanation:  DLL would be 
# 40<=>20<=>60<=>10<=>30.

#Represent a node of binary tree    
class Node:    
    def __init__(self,data):    
        self.data = data;    
        self.left = None;    
        self.right = None;    
            
class BinaryTreeToDLL:    
    def __init__(self):    
        #Represent the root of binary tree    
        self.root = None;    
        #Represent the head and tail of the doubly linked list    
        self.head = None;    
        self.tail = None;    
            
    #convertbtToDLL() will convert the given binary tree to corresponding doubly linked list    
    def convertbtToDLL(self, node):    
        #Checks whether node is None    
        if(node == None):    
            return;    
                
        #Convert left subtree to doubly linked list    
        self.convertbtToDLL(node.left);    
            
        #If list is empty, add node as head of the list    
        if(self.head == None):    
            #Both head and tail will point to node    
            self.head = self.tail = node;    
        #Otherwise, add node to the end of the list    
        else:    
            #node will be added after tail such that tail's right will point to node    
            self.tail.right = node;    
            #node's left will point to tail    
            node.left = self.tail;    
            #node will become new tail    
            self.tail = node;    
                
        #Convert right subtree to doubly linked list    
        self.convertbtToDLL(node.right);    
        
    #display() will print out the nodes of the list    
    def display(self):    
        #Node current will point to head    
        current = self.head;    
        if(self.head == None):    
            print("List is empty");    
            return;    
        print("Nodes of generated doubly linked list: ");    
        while(current != None):    
            #Prints each node by incrementing pointer.    
            print(current.data),    
            current = current.right; 
            
bt = BinaryTreeToDLL();    
#Add nodes to the binary tree    
bt.root = Node(1);    
bt.root.left = Node(2);    
bt.root.right = Node(3);    
bt.root.left.left = Node(4);    
bt.root.left.right = Node(5);    
bt.root.right.left = Node(6);    
bt.root.right.right = Node(7);    
     
#Converts the given binary tree to doubly linked list    
bt.convertbtToDLL(bt.root);    
     
#Displays the nodes present in the list    
bt.display();                        
```


```
$ python3 11btdll.py 
Nodes of generated doubly linked list: 
4
2
5
1
6
3
7
```

### **determine if a binary tree is height-balanced?**


```
# How to determine if a binary tree is height-balanced?
# A tree where no leaf is much farther away from the root than any other leaf. 
# Different balancing schemes allow different definitions of “much farther” and different amounts of work to keep them balanced.
# Consider a height-balancing scheme where following conditions should be checked to determine if a binary tree is balanced. 

# An empty tree is height-balanced. A non-empty binary tree T is balanced if: 
# 1) Left subtree of T is balanced 
# 2) Right subtree of T is balanced 
# 3) The difference between heights of left subtree and right subtree is not more than 1. 
# The above height-balancing scheme is used in AVL trees. 
# The diagram below shows two trees, one of them is height-balanced and other is not. 
# The second tree is not height-balanced because height of left subtree is 2 more than height of right subtree.
```

**The difference between heights of left subtree and right subtree is not more than 1.**

```
class Node:
    # Constructor to create a new Node
    def __init__(self, data):
        self.data = data
        self.left = None
        self.right = None
    
# function to find height of binary tree
def height(root):
     
    # base condition when binary tree is empty
    if root is None:
        return 0
    return max(height(root.left), height(root.right)) + 1

# function to check if tree is height-balanced or not
def isBalanced(root):
    
    # Base condition
    if root is None:
        return True
    
    # for left and right subtree height
    lh = height(root.left)
    rh = height(root.right)

    # allowed values for (lh - rh) are 1, -1, 0
    if (abs(lh - rh) <= 1) and isBalanced(
    root.left) is True and isBalanced( root.right) is True:
        return True
 
    # if we reach here means tree is not
    # height-balanced tree
    return False

# Driver function to test the above function
root = Node(1)
root.left = Node(2)
root.right = Node(3)
root.left.left = Node(4)
root.left.right = Node(5)
root.left.left.left = Node(8)
if isBalanced(root):
    print("Tree is balanced")
else:
    print("Tree is not balanced")
```

```
$ python3 16blheight.py 
Tree is not balanced
```

### **Find the kth smallest element in a given a binary search tree (BST)**

Write a Python program to find the kth smallest element in a given a binary search tree.

```
class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

def kth_smallest(root, k):
    stack = []
    while root or stack:
        while root:
            stack.append(root)
            root = root.left
        root = stack.pop()
        k -= 1
        if k == 0:
            break
        root = root.right
    return root.val

root = TreeNode(8)  
root.left = TreeNode(5)  
root.right = TreeNode(14) 
root.left.left = TreeNode(4)  
root.left.right = TreeNode(6) 
root.left.right.left = TreeNode(8)  
root.left.right.right = TreeNode(7)  
root.right.right = TreeNode(24) 
root.right.right.left = TreeNode(22)  

print(kth_smallest(root, 2))
print(kth_smallest(root, 3))
```

```
5
8
```


### Given the root of a binary tree, determine if it is a valid binary search tree (BST).

```
# A valid BST is defined as follows:

# The left subtree of a node contains only nodes with keys less than the node's key.
# The right subtree of a node contains only nodes with keys greater than the node's key.
# Both the left and right subtrees must also be binary search trees.

# Input: root = [2,1,3]
# Output: true

# Input: root = [5,1,4,null,null,3,6]
# Output: false
# Explanation: The root node's value is 5 but its right child's value is 4.
```

**Option1**

```
class Solution:
    def isValidateBST(self, root):
        def dfs(lower, upper, node):
            if not node:
                return true
            elif node.val<=lower or node.val >= upper:
                return False
            else:
                return dfs(lower, node.val, node.left) and dfs(node.val, upper, node.right)
        
        return dfs(float('-inf'),float('inf'),root)
```

**Option2**

```
def isValidBST(self, root):
        if not root:
            return True
        
        def inOrder(root, order):
            if root is None:
                return
            inOrder(root.left, order)
            order.append(root.val)
            inOrder(root.right, order)
        
        order = []
        inOrder(root, order)
        for i in range(1, len(order)):
            if order[i] <= order[i-1]:
                return False
        return True

```

### **Tree Order**

**preorder**

```
# 先序遍历

class Node:
    def __init__(self, data):
        self.left = None
        self.right = None
        self.data = data

# Insert Node
    def insert(self, data):

        if self.data:
            if data < self.data:
                if self.left is None:
                    self.left = Node(data)
                else:
                    self.left.insert(data)
            elif data > self.data:
                if self.right is None:
                    self.right = Node(data)
                else:
                    self.right.insert(data)
        else:
            self.data = data

# Print the Tree
    def PrintTree(self):
        if self.left:
            self.left.PrintTree()
        print(self.data),
        if self.right:
            self.right.PrintTree()
# Preorder traversal
# Root -> Left ->Right
    def PreorderTraversal(self, root):
        res = []
        if root:
            res.append(root.data)
            res = res + self.PreorderTraversal(root.left)
            res = res + self.PreorderTraversal(root.right)
        return res

root = Node(27)
root.insert(14)
root.insert(35)
root.insert(10)
root.insert(19)
root.insert(31)
root.insert(42)
print(root.PreorderTraversal(root))
```

**Inorder traversal**

```
# Inorder traversal
# Left -> Root -> Right

def inorderTraversal(self, root):
        res = []
        if root:
            res = self.inorderTraversal(root.left)
            res.append(root.data)
            res = res + self.inorderTraversal(root.right)
        return res
```

**Postorder traversal**

```
def PostorderTraversal(self, root):
        res = []
        if root:
            res = self.PostorderTraversal(root.left)
            res = res + self.PostorderTraversal(root.right)
            res.append(root.data)
        return res
```


## Stack

### Get minimum element from stack 

```
# Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

# push(x) -- Push element x onto stack.

# pop() -- Removes the element on top of the stack.

# top() -- Get the top element.

# getMin() -- Retrieve the minimum element in the stack.

# Example:

# MinStack minStack = new MinStack(); 
# minStack.push(-2); minStack.push(0); minStack.push(-3); minStack.getMin ....

```


```
class MinStack:
    def __init__(self):
        self.stack = []
        self.min = []
        self.size = 0
    
# Inserts a given element on top of the stack
    def push(self, val):
        if self.size == 0:
            self.min.append(val)
        else:
            if val <= self.min[-1]:
                self.min.append(val)
        self.stack.append(val)
        self.size += 1
    
    def pop(self):
        tmp = self.stack.pop()
        self.size -= 1
        if tmp == self.min[-1]:
            self.min.pop()
        
    def top(self):
        return self.stack[-1]
    
    def getMin(self):
        return self.min[-1]

            
if __name__ == '__main__':
     
    s = MinStack()
 
    s.push(6)
    print(s.getMin())
 
    s.push(7)
    print(s.getMin())
 
    s.push(5)
    print(s.getMin())
 
    s.push(3)
    print(s.getMin())
 
    s.pop()
    print(s.getMin())
 
    s.pop()
    print(s.getMin())
```

```
$ python3 10minstck.py 
6
6
5
3
5
6
```


### **Queue using Stacks**

```
# Queue using Stacks
# We are given a stack data structure with push and pop operations, 
# the task is to implement a queue using instances of stack data structure and operations on them.


# Python3 program to implement Queue using
# two stacks with costly deQueue()
```

```
class Queue:
    def __init__(self):
        self.s1 = []
        self.s2 = []
 
    # EnQueue item to the queue
    def enQueue(self, x):
        self.s1.append(x)
 
    # DeQueue item from the queue
    def deQueue(self):
 
        # if both the stacks are empty
        if len(self.s1) == 0 and len(self.s2) == 0:
            print("Q is Empty")
            return
 
        # if s2 is empty and s1 has elements
        elif len(self.s2) == 0 and len(self.s1) > 0:
            while len(self.s1):
                temp = self.s1.pop()
                self.s2.append(temp)
            return self.s2.pop()
 
        else:
            return self.s2.pop()
 
    # Driver code
if __name__ == '__main__':
    q = Queue()
    q.enQueue(1)
    q.enQueue(2)
    q.enQueue(3)
 
    print(q.deQueue())
    print(q.deQueue())
    print(q.deQueue())
```

### **给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效**。

```
# 有效字符串需满足：左括号必须与相同类型的右括号匹配，左括号必须以正确的顺序匹配。
# 例如，{ [ ( ) ( ) ] } 是合法的，而 { ( [ ) ] } 是非法的。
```

```
def main():
    s =  "{[()()]}"
    print(isLegal(s))
    # print(s[0])
    # print(isLeft(s))

def isLeft(c):
    if c == '{' or c == '(' or c == '[':
        return 1
    else:
        return 0

def isPair(p, curr):
    if ( p == '{' and curr == '}' ) or ( p == '(' and curr == ')') or (p == '[' and curr == ']'):
        return 1
    else:
        return 0

def isLegal(s):
    stack = []
    for i in range(0,len(s)):
        curr = s[i]
        if isLeft(curr) == 1:
            stack.append(curr)
        else:
            if len(stack) == 0:
                return "Illegal"
            p = stack.pop()
            if isPair(p, curr) == 0:
                return "Illegal"
    
    if len(stack) == 0:
        return "Legal"
    else:
        return "Illegal"

if __name__ == '__main__':
    main()
```


## Linked lists

### **Intersection Point in Y Shapped Linked Lists** 

```
# Given two singly linked lists of size N and M, 
# write a program to get the point where two linked lists intersect each other.
# Input:
# LinkList1 = 3->6->9->common
# LinkList2 = 10->common
# common = 15->30->NULL
# Output: 15

# Input: 
# Linked List 1 = 4->1->common
# Linked List 2 = 5->6->1->common
# common = 8->4->5->NULL
# Output: 8
# Explanation: 
```

```
# 4              5
# |              |
# 1              6
#  \             /
#   8   -----  1 
#    |
#    4
#    |
#   5
#   |
#   NULL  

```

```
# defining a node for LinkedList
class Node:
  def __init__(self,data):
    self.data=data
    self.next=None
     
 
 
def getIntersectionNode(head1,head2):
   
  #finding the total number of elements in head1 LinkedList
    c1=getCount(head1)
   
  #finding the total number of elements in head2 LinkedList
    c2=getCount(head2)
   
  #Traverse the bigger node by 'd' so that from that node onwards, both LinkedList
  #would be having same number of nodes and we can traverse them together.
    if c1 > c2:
        d=c1-c2
        return _getIntersectionNode(d,head1,head2)
    else:
        d=c2-c1
        return _getIntersectionNode(d,head2,head1)
   
   
def _getIntersectionNode(d,head1,head2):
     
     
    current1=head1
    current2=head2
     
     
    for i in range(d):
        if current1 is None:
            return -1
        current1=current1.next
     
    while current1 is not None and current2 is not None:
     
    # Instead of values, we need to check if there addresses are same
    # because there can be a case where value is same but that value is
    #not an intersecting point.
        if current1 is current2:
            return current1.data # or current2.data ( the value would be same)
     
        current1=current1.next
        current2=current2.next
   
  # Incase, we are not able to find our intersecting point.
    return -1
   
#Function to get the count of a LinkedList
def getCount(node):
    cur=node
    count=0
    while cur is not None:
        count+=1
        cur=cur.next
    return count
     
 
if __name__ == '__main__':
  # Creating two LinkedList
  # 1st one: 3->6->9->15->30
  # 2nd one: 10->15->30
  # We can see that 15 would be our intersection point
   
  # Defining the common node
   
  common=Node(15)
   
  #Defining first LinkedList
   
  head1=Node(3)
  head1.next=Node(6)
  head1.next.next=Node(9)
  head1.next.next.next=common
  head1.next.next.next.next=Node(30)
   
  # Defining second LinkedList
   
  head2=Node(10)
  head2.next=common
  head2.next.next=Node(30)
   
  print("The node of intersection is ",getIntersectionNode(head1,head2))
```

```
python3 12listinterction.py 
The node of intersection is  15
```

### **Reverse Linked List**

Reverse a singly linked list.

Explanation: head -> prev(None); move pointer: head -> head.next, prev -> head

**Iteratively**


```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        next = None
        while head:
            head.next, head, next = next, head.next, head
        return next # ***important***
```
 
* Recursion

 
```
 # Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        return self.reverse(head)

    def reverse(self, node, prev=None):
        if not node:
            return prev
        n = node.next
        node.next = prev

        return self.reverse(n, node)
```


## Others

### **two rectangles overlap or not**

```
#  
# Given two rectangles, find if the given two rectangles overlap or not. 
# A rectangle is denoted by providing the x and y coordinates of two points: 
# the left top corner and the right bottom corner of the rectangle. 
# Two rectangles sharing a side are considered overlapping. 
# (L1 and R1 are the extreme points of the first rectangle and L2 and R2 are the extreme points of the second rectangle).


# Input:
# L1=(0,10)
# R1=(10,0)
# L2=(5,5)
# R2=(15,0)
# Output:
# 1
# Explanation:
# The rectangles overlap.

# Input:
# L1=(0,2)
# R1=(1,1)
# L2=(-2,0)
# R2=(0,-3)
# Output:
# 0
# Explanation:
# The rectangles do not overlap.

# Python program to check if rectangles overlap
```

```
# Python program to check if rectangles overlap
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        
def doOverlap(l1, r1, l2, r2):
    # To check if either rectangle is actually a line
    # For example  :  l1 ={-1,0}  r1={1,1}  l2={0,-1}  r2={0,1}
    # the line cannot have positive overlap
    if(l1.x== r1.x or l1.y == r1.y or l2.x == r2.x or l2.y == r2.y):
        return False

    # If one rectangle is on left side of other
    if(l1.x >= r2.x or l2.x >= r1.x):
        return False

    # If one rectangle is above other
    if(r1.y >= l2.y or r2.y >= l1.y):
        return False


    return True

# Driver Code
if __name__ == "__main__":
    l1 = Point(0, 10)
    r1 = Point(10, 0)
    l2 = Point(5, 5)
    r2 = Point(15, 0)
    
    if(doOverlap(l1, r1, l2, r2)):
        print("Rectangles Overlap")
    else:
        print("Rectangles Don't Overlap")
```

```
def doOverlap(l1, r1, l2, r2):
    if(l1[0] == r1[0] or l1[1] == r1[1] or l2[0] == r2[0] or l2[1] == r2[1]):
        return False

    if(l1[0] >= r2[0] or l2[0] >= r1[0]):
        return False
    
    if(r1[1] >= l2[1] or r2[1] >= l1[1]):
        return False
    
    return True

if __name__ == "__main__":
    l1=[0, 10]
    r1=[10, 0]  
    l2=[5, 5]  
    r2=[15, 0]

    if(doOverlap(l1, r1, l2, r2)):
        print("Rectangles Overlap")
    else:
        print("Rectangles Don't Overlap")
```