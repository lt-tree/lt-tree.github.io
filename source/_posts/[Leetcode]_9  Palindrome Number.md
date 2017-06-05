---
title: Leetcode_9  Palindrome Number
date: 2017-03-30 22:39:11
tags: [Leetcode, 算法]
---

Leetcode_9  Palindrome Number


<!-- more -->
<br/>

之前做过回文字符串了，这次是回文数。
很简单的一道题，但是，要求不用额外的空间。（PS：这里指不用额外空间，空间复杂度O(1)就可以了，感觉就是表明，不让用字符数组）

<br/>

#### 1

我第一个想法就是，每次比较头尾两个数，一直向中间逼近。
事实证明，还是可以的，就是感觉速度好慢，而且代码冗长。



        class Solution {
        public:
            bool isPalindrome(int x) {
                if( x < 0 ) 
                    return false;
        
                int len = 0;
                int num = x;
                do
                {
                    num /= 10;
                    ++len;
                }while (num); 
        
                while( x )  {
                    int fj = pow(10, --len);
                    int f = x / fj;
                    int b = x % 10;
                    if( f != b )    {
                        return false;
                    }
        
                    x = (x - fj*f - b)/10;
                    --len;
                }
        
                return true;
            }
        };



<br/>

#### 2

看了看solution里的大神们，
发现一个特别短的代码，惊了个天...
就是求这个数字的逆序，然后在比较到一半的时候进行判断。
照顾到数字位数是奇数的情况，还判断了palNum/10与前半段的大小。

噢，对了；
这种方法无法判断多位数且个位是0的情况，可以在判断负数时顺便处理。


        class Solution {
        public:
            bool isPalindrome(int x) {
                if( x < 0 || ( x!=0 && x%10 == 0) )
                    return false;
        
                int palNum = 0;
                while( x > palNum ) {
                    palNum = palNum * 10 + x%10;
                    x /= 10;
                }
        
                return (x == palNum || x == palNum/10);
            }
        };
