---
title: Leetcode_20  Valid Parentheses
date: 2017-04-26 22:37:11
tags: [Leetcode, 算法]
---

Leetcode_20  Valid Parentheses


<!-- more -->
<br/>


        /**
        *  Index: 20
        *  Title: Valid Parentheses
        *  Author: ltree98
        **/


<br/>


括号配对，利用栈先进后出的特点。
当出现右半部分时，必须与之前最后出现的左半部分配对成功，否则GG。


注意几个样例：


        "["
        "]"
        "]["
        "[(])"
        "[()]"



<br/>


        class Solution {
        public:
            bool isValid(string s) {
                stack<char> container;
        
                for(int i = 0; i < s.length(); i++) {
                    switch(s[i])    {
                        case '(': 
                        case '{': 
                        case '[':   container.push(s[i]);   break;
                        case ')':   {if(container.empty() || container.top()!='(') return false; else container.pop();} break;
                        case '}':   {if(container.empty() || container.top()!='{') return false; else container.pop();} break;
                        case ']':   {if(container.empty() || container.top()!='[') return false; else container.pop();} break;
                        default: ;
                    }
                }
        
                return (container.size() == 0);
            }
        };