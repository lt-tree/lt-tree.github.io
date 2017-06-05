---
title: Leetcode_7  Reverse Integer
date: 2017-03-23 22:55:35
tags: [Leetcode, 算法]
---

Leetcode_7  Reverse Integer


<!-- more -->
<br/>

注意值范围
然后，就没了。



        /**
        *  Index: 7
        *  Title: Reverse Integer
        *  Author: ltree98
        **/

        class Solution {
        public:
            int reverse(int x) {    
                long long ans = 0;
                while( x != 0 ) {
                    int temp = x % 10;
                    ans = ans*10 + temp;
                    x /= 10;
                }
        
                if(ans > INT_MAX || ans < INT_MIN)
                    return 0;        
                return int(ans);
            }
        };