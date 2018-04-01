---
title: Leetcode_11  Container With Most Water
date: 2017-04-05 22:12:11
tags: [Leetcode, 算法]
---

Leetcode_11  Container With Most Water


<!-- more -->
<br/>


        /**
        *  Index: 11
        *  Title: Container With Most Water
        *  Author: ltree98
        **/


<br/>

题意做一个容器来存水 => 求两条线所构成的最大矩形

根据数组 [7,6,2,3,1]
先将数组根据索引和内容，构成一个点(注意索引从1开始)     =>  (1,7)、(2,6)、(3,2)、(4,3)、(5,1)
然后将这些点分别与各自(索引,0)构成的点连接。            =>  (1,7) 与 (1,0); (2,6) 与 (2,0) 等
其实，画一个坐标系，把点标记上就特别明显了。
题目中提示了，容器不可以倾斜，意思就是，长方形的宽度是取两条线段的较短边。

当然，O(n^2) 是超时的。（我不会说我傻呵呵的试了，而且还剪枝了）

其实就是求矩形的面积，矩形的面积是由 长与宽来决定的。
因此，我们从两边开始聚拢到中间，保证了长度由最长到最短，保证长度的前提求面积，
然后再以缩短长度的代价，去寻找更长的宽度，再求面积。
这期间就一定会出现最大的面积。



        class Solution {
        public:
            int maxArea(vector<int>& height) {
                int maxWater = 0;
                int h = 0, left = 0, right = height.size()-1;
        
                while(left < right) {
                    if(height[left] < height[right])
                        h = height[left++];
                    else
                        h = height[right--];
                    maxWater = max(maxWater, (right-left+1)*h);
                }
        
                return maxWater;
            }
        };
