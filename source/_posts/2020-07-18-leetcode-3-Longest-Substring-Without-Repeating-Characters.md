---
title: leetcode 3 Longest Substring Without Repeating Characters
date: 2020-07-18 12:43:00
categories: 题解
tags:
- hash table
- 滑动窗口
copyright: true
---



## 题意

[题目链接](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)，找出没有重复字符的最长子字符串。

Given a string, find the length of the **longest substring** without repeating characters.

**Example 1:**

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

**Example 2:**

```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

## 方法1 HashSet

首先记录下自己的做法。

### 思路

使用一个双指针记录当前字符串的起始位置和结束位置，然后使用一个哈希表来记录当前字符串的出现位置。当出现一个重复字符串的时候，字符串的起始位置变更为上一个重复字符串的下一个位置，然后将旧起始位置与新起始位置之间的字符从哈希表中删除。

### 复杂度

总共有两层for循环遍历，因此时间复杂度为$O(n^2)$,占用的空间为$O(n)$

### 代码

```cc
/*
Runtime: 1720 ms, faster than 5.05% of C++ online submissions for Longest Substring Without Repeating Characters.
Memory Usage: 141.1 MB, less than 5.86% of C++ online submissions for Longest Substring Without Repeating Characters.
 */
class Solution {
  public:
	int lengthOfLongestSubstring(string s) {
		int len = s.length();
		if (len == 1) return 1;
		int maxLen = 0;
		int start, end = 0;
		unordered_map<char, int> memo;
		for (int i = 0; i < len; i++) {
			if (memo.count(s[i])) {
				start = memo[s[i]] + 1;
				memo.clear();
				for (int j = start; j <= end; j++) {
					memo[s[j]] = j;
				}
			}
			memo[s[i]] = i;
			end = i;
			maxLen = max(maxLen, end - start + 1);
		}
		return maxLen;
	}

  private:
};
```

## 滑动窗口

### 思路

滑动窗口的思路也是利用双指针，只是在确定起始位置的时候需要进行一次比较才能确定。

ASCII中的字符总共只有128个，所以可以直接用128长度的vector来存储每个字符的出现位置，当作哈希表memo使用

考虑两种情况，第一种两个重复字符之间没有重复字符比如“abcdaef”，当遍历到最后一个a的时候起点为第一个a的下一位。
$$
start = memo[s[i]] + 1
$$
第二种情况，两个重复字符之间有重复字符出现比如"abbcda"。这种情况的合法起点是第二个b而不是第一个b。可以这么理解，在遍历的时候一定会先出现第一种情况，这个时候的起始点已经移动到后面了。在遇到第二种情况时，如果按照第一种方式计算出来的起点位置start一定小于当前的起始点。所以保持不变就可以了。

综上起始点的确认只需$max(start, memo[s[i]]+1)$ 就可以了。

### 复杂度

不需要再额外的一层循环操作哈希表，因此时间复杂度$O(n)$,空间复杂度$O(128)$

### 代码

```cc
/*
Runtime: 20 ms, faster than 77.56% of C++ online submissions for Longest Substring Without Repeating Characters.
Memory Usage: 7.5 MB, less than 78.82% of C++ online submissions for Longest Substring Without Repeating Characters.
 */
class Solution2 {
  public:
	int lengthOfLongestSubstring(string s) {
		const int n = s.length();
		int ans = 0;
		vector<int> idx(128, -1);
		for (int i = 0, j = 0; j < n; ++j) {
			i = max(i, idx[s[j]] + 1);
			ans = max(ans, j - i + 1);
			idx[s[j]] = j;
		}
		return ans;
	}
};
```



## 参考

https://www.bilibili.com/video/BV1CJ411G7Nn?from=search&seid=15860226297242031404