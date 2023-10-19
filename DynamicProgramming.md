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

## Regular Expression Matching

Given an input string s and a pattern p, implement regular expression matching with support for `'.'` and `'*'` where:

- '.' Matches any single character.​​​​
- '*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

Example 1:
```
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

Example 2:
```
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

Example 3:
```
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

### Solution:

```
class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.length(), n = p.length();
        vector<vector<int>> dp(m+1, vector<int>(n+1, false));

        dp[0][0]=true;

        for(int j=2;j<=n;j++){
            if(p[j-1]=='*') dp[0][j]=dp[0][j]||dp[0][j-2];
        }

        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if(s[i-1]==p[j-1] || p[j-1]=='.'){
                    dp[i][j]=dp[i-1][j-1];
                }
                else if(p[j-1]=='*'){
                    dp[i][j]=dp[i][j-1];
                    if((i>=2 && p[j-2]=='.') || (j>=2 && s[i-1]==p[j-2])) dp[i][j] = dp[i][j] || dp[i-1][j];
                    if(j>=2) dp[i][j] = dp[i][j] || dp[i][j-2];
                }
                else dp[i][j]=false;
            }
        }

        return dp[m][n];
    }
};
```
