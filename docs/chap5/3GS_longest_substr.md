# **3、Longest substring**

Given a string s, find the length of the longest substring without repeating characters.

* Input: s = "abcabcbb"
* Output: 3
* Explanation: The answer is "abc", with the length of 3.


* Input: s = "bbbbb"
* Output: 1
* Explanation: The answer is "b", with the length of 1.



* Input: s = "pwwkew"
* Output: 3
* Explanation: The answer is "wke", with the length of 3.

Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.


```
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        size = len(s)
        if size < 2:
            return size
        
        current_longest = 0
        sub = ""
        
        # for char in string directly
        for c in s:
            if c not in sub:
                sub += c
                if len(sub) > current_longest:
                    current_longest = len(sub)
            else:
                sub = f'{sub.split(c)[1]}{c}'
                
                # str = 'pwwkewp'
                # print(f"{str.split('p')[1]}{'p'}")
                # output: wwkewp
                
                # str = 'pww'
                # print(f"{str.split('w')[1]}{'w'}")
                # output: w
                # print(f"{str.split('w')[1]}")
                # output: empty

        return current_longest

str = 'pwwkew'
s = Solution()
print(s.lengthOfLongestSubstring(str)) 
```


    
    
    