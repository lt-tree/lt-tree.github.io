---
title: Leetcode_14  Longest Common Prefix
date: 2017-04-12 23:03:11
tags: [Leetcode, 算法]
---

Leetcode_14  Longest Common Prefix


<!-- more -->
<br/>


        /**
        *  Index: 14
        *  Title: Longest Common Prefix
        *  Author: ltree98
        **/


<br/>


题意就是在一个字符串集合中找出公共前缀。
其实就是遍历，
每个都进行比较，
当有不同的出现，直接返回即可。
没有什么难度。

<br/>


        class Solution {
        public:
            string longestCommonPrefix(vector<string>& strs) {
                string ans = "";
                for(int index = 0; index <= strs.size() && strs.size(); index++)    {
                    for(int j = 0; j < strs.size(); j++)
                        if(index >= strs[j].size() ||(j > 0 && strs[j][index] != strs[j-1][index]))
                            return ans;
                    ans += strs[0][index];
                }
                return ans;
            }
        };

