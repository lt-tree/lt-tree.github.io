---
title: Leetcode_29  Divide Two Integers
date: 2017-05-30 22:20:00
tags: [Leetcode, 算法]
---

Leetcode_29  Divide Two Integers


<!-- more -->
<br/>


        /**
        *  Index: 29
        *  Title: Divide Two Integers
        *  Author: ltree98
        **/


<br/>


除法的模拟运算。
最简单的肯定是，除数一直减去被除数，直到除数小于被除数。


        while(dividend >= divisor)  {
            dividend -= divisor;
            ++ans;
        }


这种方法肯定会超时（我不会说我试过。。。）
而且，显然题主不会是想让我们用这种方法来做，
题目中有明示了 division and mod operator（用位运算分解）

任何一个数都可以用以2的幂为底的数字序列之和。
比如：17 = 2^4*1 + 2^3*0 + 2^2*0 + 2^1*0 + 2^0*1

所以，就出现下面的这个解法，先找到最高可以减去的2的幂指数。
以17为例，找到4，
17 - 2^4*1 = 1
然后，再将1用3，用2，用1，用0，依次逐分。

最最后，注意一下数据处理。
首先，就是最小值，最大值部分。
然后，就是最后答案的正负值。



        class Solution {
        public:
            int divide(int dividend, int divisor) {
                if(dividend == INT_MIN)
                    if(divisor == 1)
                        return INT_MIN;
                    else if(divisor == -1)
                        return INT_MAX;
                else if(abs(divisor) == 1)
                    return dividend*divisor;
        
                long dividendTemp = dividend;
                long divisorTemp = divisor;     
                int isNegative = 1;
                if(dividendTemp < 0)    {
                    dividendTemp = abs(dividendTemp);
                    isNegative *= -1;
                }
                if(divisorTemp < 0) {
                    divisorTemp = abs(divisorTemp);
                    isNegative *= -1;
                }
        
                int maxFactor = 0;
                while(divisorTemp <= (dividendTemp>>1)) {
                    divisorTemp <<= 1;
                    ++maxFactor;
                }
        
                long ans = 0;
                while(maxFactor >= 0)   {
                    if(dividendTemp >= divisorTemp) {
                        ans += 1<<maxFactor;
                        dividendTemp -= divisorTemp;
                    }
                    divisorTemp >>= 1;
                    --maxFactor;
                }
        
        
                if(ans >= INT_MAX)
                    return INT_MAX;
                return ans*isNegative;
            }
        };