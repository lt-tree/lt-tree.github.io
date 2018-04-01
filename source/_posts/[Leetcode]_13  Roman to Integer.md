---
title: Leetcode_13  Roman to Integer
date: 2017-04-10 22:03:11
tags: [Leetcode, 算法]
---

Leetcode_13  Roman to Integer


<!-- more -->
<br/>


        /**
        *  Index: 13
        *  Title: Roman to Integer
        *  Author: ltree98
        **/


<br/>

#### 1

<br/>

之前是数字转罗马数，现在反过来了，罗马数转数字。（1~3999）
思路同数字转罗马，
将 valueToRoman数组从第一个比较到最后一个，然后符合情况时将对应的值加起来。

<br/>


        class Solution {
        public:
            int romanToInt(string s) {
                int values[13] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
                string valueToRoman[13] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        
                int startPos = 0, len = s.length();
                int ans = 0;
                for(int i = 0; i < 13; i++) {
                    while( startPos < len ) {
                        string tempS = s.substr(startPos, valueToRoman[i].length());
                        if(tempS == valueToRoman[i])    {
                            ans += values[i];
                            startPos = startPos + tempS.length();
                        }
                        else
                            break;
                    }
                }
                return ans;        
            }
        };


<br/>

#### 2

<br/>

在讨论组又发现一个更简单的。
根据罗马数的规则：小的数放在右边是加小的数，小的数放在左边是减去小的数。
所以就从右向左把数字加起来，如果发现当前的数比右边的数小，就减去这个数的值。
比如: IXVI
I -> V -> X 每个都比右面的数字大（V > I, X > V), 所以 XVI就是 10 + 5 + 1 = 16。
但是，接下来 I < X, 这时候就需要将总和把 I 的这部分减去： IXVI = 16 - 1 = 15。
这个例子只是为了说明方法， 正常来说15 应该是 XV 。

<br/>


        class Solution {
        private:
            map<char, int> comparedMap = {{'I', 1}, {'V', 5}, {'X', 10}, {'L', 50}, {'C', 100}, {'D', 500}, {'M', 1000}, };
        
        public:
            int romanToInt(string s) {        
                int ans = comparedMap[s[s.length()-1]];
        
                for(int i = s.length()-2; i >= 0; i--)  {
                    if( comparedMap[s[i+1]] > comparedMap[s[i]] )   {
                        ans -= comparedMap[s[i]];
                    }
                    else    {
                        ans += comparedMap[s[i]];
                    }
                }
        
                return ans;        
            }
        };