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

## Generate Parentheses

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

Example 1:
```
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

Example 2:
```
Input: n = 1
Output: ["()"]
```

Constraints: `1 <= n <= 8`

### Solution:

**Solution One**

Given a number `n`, we have to generate all valid `n` pairs of parenthesis. Since we have to generate all the valid combinations, the first solution which comes to our mind is backtracking (recursion). Given the constraint, I would say that is the best solution.

We start with number of open brackets `open = 0`, and number of close brackets `close = 0`. Now at any given recursion level, either we can put one open bracket or one close bracket. The constraints would be that open can never be greater than `n` and that `close < open` at all times. Below code is self explanatory. Note I used string references to obtain a gain in speed.

```
class Solution {
public:
    void util(vector<string>& res, int open, int close, string& tmp, int n)
    {
        if(tmp.length()==2*n) {res.push_back(tmp); return;}
        if(open<n){
            tmp.push_back('(');
            util(res,open+1,close,tmp,n);
            tmp.pop_back();
        }
        if(close<open){
            tmp.push_back(')');
            util(res,open,close+1,tmp,n);
            tmp.pop_back();
        }
    }
    vector<string> generateParenthesis(int n) {
        int open=0,close=0; // open -> number of open brackets
						 // close -> number of close brackets
        vector<string> res;
        if(n==0) return res;
        string temp="";
        util(res,open,close,temp,n);
        return res;
    }
};
```

`Time Complexity` - Very important for these kind of questions. Since recursion is a tree, and here there are two recursive calls possible at any level. Heigh of the tree will be 2n, since we are generating `2*n` number of brackets. So, worst-case Time Complexity will be `O(2^(2n))`.

Space Complexity - `O(2*n)` (Stack space after using recursion)

**Solution Two**

Now, given the constraints in the problem, backtracking is good. But what if, n is large. We can't afford exponential solution. The next thought should be dynamic programming, and if there is a overlapping subproblems nature to it.

Suppose `dp[i]` contains all the valid parentheses possible of length `2*i`. Suppose you got `dp[2]` which is `{ (()) , ()() }`. Now what will be `dp[3]`? It can be written as -

```
( + dp[0] + ) + dp[2] = ()(()) and ()()()
( + dp[1] + ) + dp[1] = (())()
( + dp[2] + ) + dp[0] = ((())) and (()())
```

So you see, we have an overlapping subproblems structure. It's good to know here that this structure closely follows Catalan Numbers. (You can find the number of valid parentheses using Catalan Numbers).

`dp[i] = "(" + dp[j] + ")" + dp[i-j-1]` (Recursive relation. Similar to a binary tree generation).

```
vector<string> generateParenthesis(int n) {
        vector<vector<string>> dp(n+1); // cache to store all generated strings
        dp[0] = {""}; 
        for(int i=1;i<=n;i++){
            for(int j=0;j<i;j++){
                vector<string> left = dp[j];
                vector<string> right = dp[i-j-1];
                for(int k=0;k<left.size();k++){
                    for(int l=0;l<right.size();l++){
                        dp[i].push_back("(" + left[k] + ")" + right[l]);
                    }
                }
            }
        }
        return dp[n];
    }
```

Time Complexity - `O(n^4)`.
Space Complexity - `O(n^2)`.

## Longest Valid Parentheses

Given a string containing just the characters '(' and ')', return the length of the longest valid (well-formed) parentheses 
substring.

Example 1:
```
Input: s = "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()".
```

Example 2:
```
Input: s = ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()".
```

Example 3:
```
Input: s = ""
Output: 0
```

### Solution:

**Dynamic Programming solution** -

```
class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.length(), ma = 0;
        vector<int> dp(n+1, 0);

        for(int i=1;i<n;i++){
            if(s[i]==')'){
                if (s[i-1]=='('){
                    dp[i+1]=dp[i-1]+2;
                }
                else{
                    if((i-dp[i])>=1 && s[i-1-dp[i]]=='('){
                        dp[i+1]=dp[i]+dp[max(0,i-dp[i]-1)]+2;
                    }
                }
            }
            ma=max(ma,dp[i+1]);
        }

        for (auto a:dp) cout << a << " ";
        return ma;
    }
};
```

**Stack Solution** -

The workflow of the solution is as below.

- Scan the string from beginning to end.
- If current character is `'('`, push its index to the stack. If current character is `')'` and the character at the index of the top of stack is `'('`, we just find a matching pair so pop from the stack. Otherwise, we push the index of `')'` to the stack.
- After the scan is done, the stack will only contain the indices of characters which cannot be matched. Then let's use the opposite side - substring between adjacent indices should be valid parentheses.
- If the stack is empty, the whole input string is valid. Otherwise, we can scan the stack to get longest valid substring as described in step 3.

```
class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.length(), longest = 0;
        stack<int> st;
        for (int i = 0; i < n; i++) {
            if (s[i] == '(') st.push(i);
            else {
                if (!st.empty()) {
                    if (s[st.top()] == '(') st.pop();
                    else st.push(i);
                }
                else st.push(i);
            }
        }
        if (st.empty()) longest = n;
        else {
            int a = n, b = 0;
            while (!st.empty()) {
                b = st.top(); st.pop();
                longest = max(longest, a-b-1);
                a = b;
            }
            longest = max(longest, a);
        }
        return longest;
    }
};
```

## Trapping Rain Water:

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

Example 1:
```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

Example 2:
```
Input: height = [4,2,0,3,2,5]
Output: 9
```

Constraints:
```
n == height.length
1 <= n <= 2 * 104
0 <= height[i] <= 105
```

### Solution:

```
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size(), tot = 0, right=height[n-1];
        vector<int> left(n, 0);
        
        for(int i=0;i<n;i++){
            if(!i) left[i]=height[i];
            else left[i]=max(left[i-1], height[i]);
        }
        
        tot+=min(left[n-1],right)-height[n-1];
        for(int i=n-2;i>=0;i--){
            right=max(right, height[i]);
            tot+=min(left[i],right)-height[i];
        }

        return tot;
    }
};
```
