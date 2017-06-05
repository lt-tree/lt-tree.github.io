---
title: Leetcode_31  Next Permutation
date: 2017-06-04 23:54:00
tags: [Leetcode, 算法]
---

Leetcode_31  Next Permutation


<!-- more -->
<br/>


        /**
        *  Index: 31
        *  Title: Next Permutation
        *  Author: ltree98
        **/


<br/>


有必要说一下题意，我当时都没看明白这题。

先了解一下几个定义

1. 全排列，
1,2,3 三个数的全排列有：（字典序）
1 2 3
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
6种（对于n个不同数的全排列有 f(n) = n! 种）

2. 字典序
字典序就是对给定的字符集中的字符规定一个先后关系。这里的数字可以看做小数在大数前面。
还有一种理解方法，你给上面每个数值相应位置定一个权值。
比如第一个位置（最前面的）权值最大，为100；第二个为10；第三个为1。
1 2 3 = 123
1 3 2 = 132
2 1 3 = 213
2 3 1 = 231
3 1 2 = 312
3 2 1 = 321
字典序，就可以看做是相应位置的数字乘以权值的和，按照最后得数的由小到大的顺序。

<br/>

这道题，是求下一个字典序的全排列。（因为题目中对于全排列规定了顺序，所以就可以求前一个或者后一个）
可以看出 1 2 3 下一个 就是 1 3 2 再下一个就是 2 1 3。


<br/>

#### 模拟法

从后向前，先比较前后大小，如果有前面的小于后面的，那就以那个位置为中心，可以进行修改。
以 1 2 4 3 2 为例
第二个位置 小于 第三个位置。
找后面有没有比第二个数字大的最小的数（有点绕，就是找所有比2大的数，在这些里面取最小的）
然后将 第二个位置的数 与 上面找到的数 替换位置。
最后，对第二个位置右面的数列进行有小到大排序。

1. 从后向前比较大小，如果前面的数小于后面，进入第2步；否则，逆序输出。
2. 从当前数字及向右的序列中找出比左面数字大的最小数字及序列号
3. 交换数值
4. 对当前数字及向右的序列进行由小到大排序


        class Solution {
        private:
            void findNextNumIndexFromRight(std::vector<int> nums, int& index, int& minDerta)    {
                int standIndex = index-1;
                for(int i = index; i < nums.size(); i++)    {
                    int derta = nums[i] - nums[standIndex];
                    if(derta > 0 && (derta < minDerta || minDerta <= 0))    {
                        index = i;
                        minDerta = derta;
                    }
                }
            }
        
            void swapNum(int& a, int& b)    {
                a ^= b;
                b ^= a;
                a ^= b;
            }
        public:
            void nextPermutation(vector<int>& nums) {
                int len = nums.size();
                for(int i = len-1; i > 0; i--)  {
                    if(nums[i-1] < nums[i]) {
                        int minIndex = i, minDerta = nums[i] - nums[i-1];
                        findNextNumIndexFromRight(nums, minIndex, minDerta);
                        swapNum(nums[i-1], nums[minIndex]); 
        
                        // 此处用的冒泡排序
                        for(int j = i; j < len; j++)
                            for(int k = len-1; k > j; k--)
                                if(nums[k] < nums[k-1]) 
                                    swapNum(nums[k-1], nums[k]);
        
                        return;
                    }
                }
        
                reverse(nums.begin(), nums.end());
                return;
            }
        };


<br/>

#### 稍微改进一下


        class Solution {
        private:
            int findNextNumIndexFromRight(std::vector<int> nums, int index) {
                for(int i = nums.size()-1; i > index; i--)
                    if(nums[i] > nums[index])
                        return i;
            }
        
            void swapNum(int& a, int& b)    {
                a ^= b;
                b ^= a;
                a ^= b;
            }
        public:
            void nextPermutation(vector<int>& nums) {
                int len = nums.size();
                for(int i = len-1; i > 0; i--)  {
                    if(nums[i-1] < nums[i]) {
                        int minIndex = findNextNumIndexFromRight(nums, i-1);
                        swapNum(nums[i-1], nums[minIndex]); 
                        reverse(nums.begin()+i, nums.end());
                        return;
                    }
                }
        
                reverse(nums.begin(), nums.end());
                return;
            }
        };


<br/>

##### 来几组测试数据吧


        [1,2,3]
        [1,3,2]
        [1,1,5]
        [3,2,1]
        [1,2,3,2,1]
        [5,4,7,5,3,2]
        [5,4,7,4,3,2]