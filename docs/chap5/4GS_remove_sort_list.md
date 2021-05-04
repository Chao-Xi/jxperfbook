# **4、Remove duplicate in Sort List**

* Given the head of a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list. 
* Return the linked list sorted as well.



* Input: head = [1,2,3,3,4,4,5]
* Output: [1,2,5]


* Input: head = [1,1,1,2,3]
* Output: [2,3]


```
class Solution:
    def deleteDuplicates(self,ls):
        
        # for i in range(0, len(list)):
        #     print(i)
        list1 = list(set(ls))
        # remove duplicate
        
        dupes = []
        seen = {}
        
        for x in ls:
            if x not in seen.keys():
                seen[x] = 1
            else:
                if seen[x] == 1:
                    dupes.append(x)
                seen[x] += 1
        
        for x in dupes:
            if x in list1:
                list1.remove(x)
        
        return list1
        
        
s = Solution()
s.deleteDuplicates([1,2,3,3,4,4,5])
print(s.deleteDuplicates([1,2,3,3,4,4,5]))

# [1, 2, 5]
```



```
class Solution:
    def deleteDuplicates(self, ListNode):
        holder = ListNode(0, head)
        dummy_head = holder
        next_node = head
        
        while next_node:
            if next_node.next is not None and next_node.next.val == next_node.val:
                while(next_node.next is not None and next_node.val == next_node.next.val):
                    next_node = next_node.next
                dummy_head.next = next_node.next
            else:
                dummy_head = dummy_head.next
        next_node = next_node.next
    
        return holder.next

s = Solution()
s.deleteDuplicates([1,2,3,3,4,4,5])
print(s.deleteDuplicates([1,2,3,3,4,4,5]))
```


       