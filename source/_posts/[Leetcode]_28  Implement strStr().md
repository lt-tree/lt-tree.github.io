---
title: Leetcode_28  Implement strStr()
date: 2017-05-30 22:17:00
tags: [Leetcode, 算法]
---

Leetcode_28  Implement strStr()


<!-- more -->
<br/>


        /**
        *  Index: 28
        *  Title: Implement strStr()
        *  Author: ltree98
        **/


<br/>


字符串的模式匹配

通过这一道题，可以练习一下这些匹配算法。

<br/>

#### BF算法（Brute Force - 暴风算法）   时间复杂度 O(m*n)
最普通的模式匹配算法。
依次比较，如果匹配失败，从头再来。
可以看出，是一个很暴力的算法。

加了两个小剪枝，
1. 如果匹配串长度大于原串，直接返回-1
2. 如果两串长度相等，直接判断值是否相等，返回相应值。（不加会TLE）
3. 如果匹配串为空，返回0



        class Solution {
        public:
            int strStr(string haystack, string needle) {
                int lenHs = haystack.length();
                int lenNe = needle.length();
                if(lenHs < lenNe)
                    return -1;
                else if(lenHs == lenNe)
                    if(haystack == needle)
                        return 0;
                    else
                        return -1;
                else if(lenNe == 0)
                    return 0;
    
                int i = 0, j = 0;
                while(i < lenHs)  {
                    if(haystack[i] == needle[j])  {
                        ++i;
                        ++j;
    
                    if(j == lenNe)
                        return i - j;
                    }
                    else  {
                        i = i - j + 1;
                        j = 0;
                    }
                }
    
                return -1;
            }
        };


<br/>

#### KMP算法  时间复杂度：O(m+n)

举个板栗：
原串：abababc | 匹配串是：ababc
当我比较到原串的第五个字符a 与 匹配串的第五个字符c，发现匹配失败；
这时，我不需要从原串第二个字符b 与 匹配串第一个字符a，进行比较；
只需要将原串的第三个字符a 与 匹配串的第三个字符a，进行比较；
因为它们前缀是部分重复的。

这个特性是由匹配串来决定的，所以，KMP算法，重要在于求出匹配失败后，跳回的部分，即 求出匹配串的Next数组。

next数组如何求呢？
1. 第一个字符next值设置为-1
2. 是从前往后推到的，就是next[1] 是由str[0] 与 next[0] 决定的
3. 如果前一个数的next值为-1，则此next值为0（-1 + 1）
4. 如果前一个数next值不为-1，比较前一个字符 与 前next值对应的字符；如果字符相等，则next值为前一个next值+1；如果不等则继续往前比较。
    以 index = 2 为例，应该比较 str[1](b) 与 str[next[1]](b)，相等，所以 next[2] = next[1] + 1 = 1
    以 index = 3 为例, 比较 str[2](a) 与 str[next[2]](b), 不相等， 继续比较 str[2](a) 与 str[0](next[index = 2] -> index 1 -> 因为不相等 -> next[index = 1] -> index = 0), 也不相等，但是此时 next[index] 为-1，所以next[3] = 0(-1 + 1)


index     0   1   2   3   4   5   6
str       b   b   a   b   b   a   
next      -1  0   1   0   1   2   3 



        class Solution {
        private:
            int NEXT[100001];
      
        public:
            void createNextArray(string str, int len) {
                int i = 0, j = -1;
    
                NEXT[0] = -1;
                while(i < len)  {
                if(j == -1 || str[i] == str[j]) 
                    NEXT[++i] = ++j;
                else
                    j = NEXT[j];
                }
            }
    
            int strStr(string haystack, string needle) {
                int lenHs = haystack.length();
                int lenNe = needle.length();
                if(lenHs < lenNe)
                    return -1;
                else if(lenHs == lenNe)
                    if(haystack == needle)
                        return 0;
                    else
                        return -1;
                else if(lenNe == 0)
                    return 0;
                    
                memset(NEXT, -1, sizeof(NEXT));        
                createNextArray(needle, lenNe+1);
    
                int i = 0, j = 0;
                while(i < lenHs) {
                    if(haystack[i] == needle[j])  {
                        ++i;
                        ++j;
    
                        if(j == lenNe)
                            return i - j;
                    }
                    else
                        if(NEXT[j] == -1) {
                            ++i;
                            j = 0;
                        }
                        else
                            j = NEXT[j];
                }
    
                return -1;
            }
        };