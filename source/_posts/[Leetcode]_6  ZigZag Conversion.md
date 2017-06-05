---
title: Leetcode_6  ZigZag Conversion
date: 2017-03-27 22:48:00
tags: [Leetcode, 算法]
---

Leetcode_6  ZigZag Conversion
模拟法，找规律


<!-- more -->
<br/>

其实，这题本质上应该算是找规律的题目。
按特定的要求输出字符串。

<br/>

#### 1.模拟法

将一长串字符，分成n行，可以根据要求，把每行分别求出来，最后合并成一行输出。
也就是说 ABCDEFGHIJKLMN 分成4行。
模拟它的之字形走位，4行将分别为:
        

        A   G     M
        B  F H   L N
        C E   I K
        D      J


也就是:


        AGM
        BFHLN
        CEIK
        DJ


按照它的走位，可以定一个头部，到特定位置，让头部改变方向（此题方向只有两个，上和下），最后合并即可。


        class Solution {
        public:
            string convert(string s, int numRows) {
                int len = s.length();
                if( numRows == 1 or len <= numRows )
                    return s;
        
                string temp[10001];
                int dir = 1;
                int tIndex = 0;
                int index = 0;
        
                while( index < len ) {
                    temp[tIndex] += s[index];
                    if( dir == 1 and tIndex >= numRows-1 )    {
                        dir = -1;
                    }
                    else if( dir == -1 and tIndex <= 0 ) {
                        dir = 1;
                    }
                    tIndex = tIndex + dir;
                    index++;
                }
        
                string ans = "";
                for(int i = 0; i < numRows; i++)    {
                    ans += temp[i];
                }
        
                return ans;
            }
        };

<br/>


#### 2.找规律

这种题，模拟法一般都能过，如果有更好的方法，那就是找规律。
这题，一看就觉得是有规律的，最简单的最上面和最下面一行，间隔数基本是固定的： 2\*n-2(n 就是要求列的行数)
最主要的是第一行和最后一行中间部分的规律。
中间部分的规律是: 2\*n-2-2\*i
具体如何找规律?
其实我也没啥窍门，可以试试多划拉划拉，多找几个例子....



        class Solution {
        public:
            string convert(string s, int numRows) {
                int len = s.length();
                if( len == 0 || numRows < 2 )
                    return s;

                string ans = "";
                int rtrn = 2 * numRows - 2;
                for(int tIndex = 0; tIndex < numRows; tIndex++)    {
                    for(int index = tIndex; index < len; index+=rtrn)   {
                        ans += s[index];

                        if(tIndex > 0 && tIndex < numRows-1)    {
                            int midIndex = index + rtrn - 2*tIndex;
                            if(midIndex < len)
                                ans += s[midIndex];
                        }
                    }
                }
                return ans;
            }
        };
