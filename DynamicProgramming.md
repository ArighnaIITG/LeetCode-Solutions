## Longest Palindromic Substring

Given a string s, return the longest palindromic substring in s.

Example 1:

```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

Example 2:

```
Input: s = "cbbd"
Output: "bb"
```
### Solution

Intuitively, we list all the substrings, pick those palindromic, and get the longest one. This approach takes O(n^3) time complexity, where n is the length of s.

**Dynamic Programming**

The problem can be broken down into subproblems which are reused several times. Overlapping subproblems lead us to Dynamic Programming.

We decompose the problem as follows:

- State variable - The `start` index and `end` index of a substring can identify a state, which influences our decision. So the state variable is `state(start, end)` indicates whether s[start, end] inclusive is palindromic
- Goal state - `max(end - start + 1)` for all `state(start, end) = true`
- State transition

```
for start = end (e.g. 'a'), state(start, end) is True
for start + 1 = end (e.g. 'aa'), state(start, end) is True if s[start] = s[end]
for start + 2 = end (e.g. 'aba'),  state(start, end) is True if s[start] = s[end] and state(start + 1, end - 1)
for start + 3 = end (e.g. 'abba'),  state(start, end) is True if s[start] = s[end] and state(start + 1, end - 1)
...
```

This approach takes O(n^2) time complexity, O(n^2) space complexity, where n is the length of s.

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        dp = [[False] * n for _ in range(n)]
        for i in range(n):
            dp[i][i] = True
        longest_palindrome_start, longest_palindrome_len = 0, 1

        for end in range(0, n):
            for start in range(end - 1, -1, -1):
                # print('start: %s, end: %s' % (start, end))
                if s[start] == s[end]:
                    if end - start == 1 or dp[start + 1][end - 1]:
                        dp[start][end] = True
                        palindrome_len = end - start + 1
                        if longest_palindrome_len < palindrome_len:
                            longest_palindrome_start = start
                            longest_palindrome_len = palindrome_len
        return s[longest_palindrome_start: longest_palindrome_start + longest_palindrome_len]
```

Two pointers
This approach takes `O(n^2)` time complexity, `O(1)` space complexity, where n is the length of s.

For each character `s[i]`, we get the first character to its right which isn't equal to `s[i]`.
Then `s[i, right - 1]` inclusive are equal characters e.g. "aaa".
Then we make `left = i - 1`, while `s[left] == s[right]`, `s[left, right]` inclusive is palindrome, and we keep extending both ends by `left -= 1, right += 1`.
Finally we update the tracked longest palindrome if needed.

```
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        longest_palindrome_start, longest_palindrome_len = 0, 1

        for i in range(0, n):
            right = i
            while right < n and s[i] == s[right]:
                right += 1
            # s[i, right - 1] inclusive are equal characters e.g. "aaa"
            
            # while s[left] == s[right], s[left, right] inclusive is palindrome e.g. "baaab"
            left = i - 1
            while left >= 0 and right < n and s[left] == s[right]:
                left -= 1
                right += 1
            
            # s[left + 1, right - 1] inclusive is palindromic
            palindrome_len = right - left - 1
            if palindrome_len > longest_palindrome_len:
                longest_palindrome_len = palindrome_len
                longest_palindrome_start = left + 1
            
        return s[longest_palindrome_start: longest_palindrome_start + longest_palindrome_len]
```     
