---
title: Leetcode_3  Longest Substring Without Repeating Characters
date: 2017-03-16 21:43:35
tags: [Leetcode, 算法]
---

Leetcode_3  Longest Substring Without Repeating Characters
简单题

<!-- more -->
<br/>


一个checkMap来存储某字符是否出现过
时间复杂度O(n^2), 空间复杂度O(n)


    class Solution {
    public:
        int lengthOfLongestSubstring(string s) {
            if(s.length() < 2)
                return s.length();

            int maxLen = 1;
            for(int i = 0; i < s.length(); i++) {
                int checkMap[300] = {0};
                checkMap[s[i] - ' '] = 1;
                for(int j = i + 1; j < s.length(); j++) {
                    if(checkMap[s[j] - ' '] != 0)    {
                        maxLen = max(maxLen, j - i);
                        break;
                    }
                    else if( j == s.length() - 1)
                        maxLen = max(maxLen, j - i + 1);
                    checkMap[s[j] - ' '] = 1;
                }
            }
            return maxLen;
        }
    };


<br/>
当然，还是要看一下大神们的解法。
时间复杂度O(n), 空间复杂度O(1)

设置一个checkMap 来存储，每个字符出现的位置。
如果出现同一个字符，就重置子串启始位置。
不忍看自己的代码了....



    class Solution {
    public:
        int lengthOfLongestSubstring(string s) {
            std::vector<int> checkMap(256, -1);
            int maxLen = 0, start = -1;
            for(int i = 0; i < s.length(); i++)    {
                if(checkMap[s[i]] > start)
                    start = checkMap[s[i]];
                checkMap[s[i]] = i;
                maxLen = max(maxLen, i - start);
            }
            return maxLen;
        }
    };












































