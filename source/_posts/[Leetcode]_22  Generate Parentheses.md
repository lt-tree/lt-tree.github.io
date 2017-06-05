---
title: Leetcode_22  Generate Parentheses
date: 2017-05-04 21:12:00
tags: [Leetcode, 算法]
---

Leetcode_22  Generate Parentheses


<!-- more -->
<br/>


        /**
        *  Index: 22
        *  Title: Generate Parentheses
        *  Author: ltree98
        **/


<br/>


括号配对种类
必须左右配对，不可右左。
做完以后，看了其他的解法，思维其实是一样的。


递归的解法
先放一个符合条件的，然后获得后面符合条件的，再讲这个与后面符合条件的组合。
层层返回符合条件的情况，进行组合，再向上返回。

终点就是 当左右括号剩余均为0的时候。
当然，放置括号也是有限制的，当前放置的右括号数量不可超过左括号数量。


<br/>



        class Solution {
        private:
            vector<string> generateParenthesisTemp(int l, int r)    {
                vector<string> vec;
                if(l == 0 && r == 0)    {
                    vec.push_back("");
                    return vec;
                }
        
                if(l <= r)  {
                    if(l > 0)   {
                        string temp = "(";
                        vector<string> vs = generateParenthesisTemp(l-1, r);
                        for(int i = 0; i < vs.size(); i++)
                            vec.push_back(temp+vs[i]);
                    }
        
                    if(r > 0)   {
                        string temp = ")";
                        vector<string> vs = generateParenthesisTemp(l, r-1);
                        for(int i = 0; i < vs.size(); i++)
                            vec.push_back(temp+vs[i]);              
                    }
                }
                return vec;
            }
        
        public:
            vector<string> generateParenthesis(int n) {
                return generateParenthesisTemp(n, n);
            }
        };