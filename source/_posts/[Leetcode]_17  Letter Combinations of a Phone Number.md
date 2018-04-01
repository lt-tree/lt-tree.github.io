---
title: Leetcode_17  Letter Combinations of a Phone Number
date: 2017-04-20 23:46:11
tags: [Leetcode, 算法]
---

Leetcode_17  Letter Combinations of a Phone Number
递归


<!-- more -->
<br/>


        /**
        *  Index: 17
        *  Title: Letter Combinations of a Phone Number
        *  Author: ltree98
        **/


<br/>


这道题应该是用递归来做


        digits.length() >   1  =>   将 digits[0]对应的集合 与 digits.substr(1)返回的集合连接
        digits.length() ==  1  =>   返回数字对应的字符串集合


然后再处理一些无效值的情况:

*   数字中 0, 1, *, # 等其他值。 —— 这里用他们 ASCII码的差值来判断。
*   如果输入的数字中存在无效值，整个返回空。 —— 这里判断后面字符串的返回值是否为空，若为空，直接返回空。


<br/>


        class Solution {
        private:
            string map[10] = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        
        public:
            vector<string> letterCombinations(string digits) {
                if(digits.length() == 0)    return {};
        
                int temp = digits[0] - '0';
                if( temp < 2 || temp > 9 )  return {};
        
                vector<string> ans = {};
                string mapStr = map[temp];
                if(digits.length() > 1) {
                    vector<string> preAns = letterCombinations(digits.substr(1, digits.length()-1));
                    if(preAns.size() == 0)  return {};
        
                    for(int i = 0; i < mapStr.length(); i++)    {
                        for(int j = 0; j < preAns.size(); j++)  {
                            ans.push_back(mapStr[i]+preAns[j]);
                        }
                    }
                }
                else    {
                    for(int i = 0; i < mapStr.length(); i++)    {
                        ans.push_back(mapStr.substr(i, 1));
                    }
                }
                return ans;
            }
        };


