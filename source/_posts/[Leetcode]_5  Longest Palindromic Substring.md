---
title: Leetcode_5  Longest Palindromic Substring
date: 2017-03-21 22:39:35
tags: [Leetcode, 算法]
---

Leetcode_5  Longest Palindromic Substring
DP

<!-- more -->
<br/>

如果，str 是一个回文串，那么去掉str的头和尾，它也是一个回文串：
比如 abcba -> bcb -> c 
所以，这题可通过动态规划来解。
<br/>


        class Solution {
        public:
            string longestPalindrome(string s) { 
                int len = s.length();
                bool temp[1001][1001] = {0};
        
                int startSPos = 0;
                int longestPaliLen = 1;
        
                for(int i = len - 1 ; i >= 0 ; i--)    {
                    temp[i][i] = true;
                    if( i > 0 and s[i - 1] == s[i] )    {
                        temp[i-1][i] = true;
                        startSPos = i - 1;
                        longestPaliLen = 2;
                    }
                }
        
                for(int pLen = 3 ; pLen <= len ; pLen++) {
                    for(int j = len - 1; j >= 0 ; j--)  {
                        int i = j - pLen + 1;
                        if(i < 0)
                            break;
        
                        if( temp[i+1][j-1] == true and s[i] == s[j] )   {
                            temp[i][j] = true;
                            startSPos = i;
                            longestPaliLen = pLen;
                        }
                    }
                }
        
                return s.substr(startSPos, longestPaliLen);
            }
        };


<br/>
下面这种解法的亮点在于标记 key 的那部分；
它将重复的字母跳过（因为他们一定是回文），
并且能找到以这段重复字母为中心的的最长回文串，
之后，
还让下一个查找坐标直接拉到这段重复的回文串之后。

<br/>


        class Solution {
        public:
            string longestPalindrome(string s) { 
                int len = s.length();
                if(len == 1)    
                    return s;
                
                int startPos = 0, longestPaliLen = 1;
                for(int i = 0; i < len; )    {
                    int sPos = i, ePos = i;
        
                    // key
                    while(ePos < len-1 && s[ePos+1] == s[ePos])
                        ePos++;
        
                    i = ePos+1;
                    while(ePos < len-1 && sPos > 0 && s[ePos+1] == s[sPos-1])   {
                        sPos--;
                        ePos++;
                    }
        
                    int newLen = ePos - sPos + 1;
                    if(newLen > longestPaliLen) {
                        startPos = sPos;
                        longestPaliLen = newLen;
                    }
                }
        
                return s.substr(startPos, longestPaliLen);
            }
        };
