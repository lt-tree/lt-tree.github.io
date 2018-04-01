---
title: Leetcode_8  String to Integer(atoi)
date: 2017-03-28 22:20:11
tags: [Leetcode, 算法]
---

Leetcode_8  String to Integer(atoi)


<!-- more -->
<br/>

注意处理一些细节东西，
提供一下我的测试数据吧。



        /**
        -- test case:
        ""
        "abc"
        "01234"
        "0.2"
        "-123"
        "123.a"
        "12a23"
        "abc12"
        "-123a"
        "-12-"
        "."
        "+1"
        "-+1"
        "+-1"
        "      33"
        "-      33"
        "      003"
        "+     22"
        "     +004500"
        "13+-1"
        "123   3"
        "3147483647"
        "-3147483648"
        "9223372036854775809"
        "1234             3746813578"
        **/




        class Solution {
        public:
            int myAtoi(string str) {
                int flg = 0;
                long long ans = 0;
                int len = str.length();
                for(int index = 0; index < len; index++)    {
                    int num = str[index] - '0';
                    if(str[index] == '-' or str[index] == '+')  {
                        if(flg == 0)
                            flg = ',' - str[index];
                        else
                            break;
                    }
                    else if (str[index] == ' ' and flg == 0)
                    {
                        continue;
                    }
                    else if( num >= 0 && num <= 9 ) {
                        if(flg == 0)    {
                            flg = 1;
                        }
                        ans = ans*10 + num;
                        if( ans > INT_MAX )
                            break;
                    }
                    else
                        break;
                }
        
                ans = ans * flg;
                if(ans < INT_MIN)
                    ans = INT_MIN;
                else if(ans > INT_MAX)
                    ans = INT_MAX;
                return ans;
            }
        };