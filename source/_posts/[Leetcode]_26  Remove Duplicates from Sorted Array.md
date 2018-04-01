---
title: Leetcode_26  Remove Duplicates from Sorted Array
date: 2017-05-13 21:12:00
tags: [Leetcode, 算法]
---

Leetcode_26  Remove Duplicates from Sorted Array


<!-- more -->
<br/>


        /**
        *  Index: 26
        *  Title: Remove Duplicates from Sorted Array
        *  Author: ltree98
        **/


<br/>


题目给出的几个注意点：
1. 所给的数组是排序的（很重要）
2. 其实答案是根据返回的数组长度而截取原数组所得的数组
3. 空间复杂度在O(1)

#### 记录重复数量，不重复的序列号，当前比较的数值

借助几个变量存储上述的值，在每次比较数值变化的时候，来改变序列号与比较数值，其他时候递增重复数值，即可。



<br/>



        class Solution {
        public:
            int removeDuplicates(vector<int>& nums) {
                int index = 0, cur = INT_MIN, repeat = 0, len = nums.size();
        
                for(int i = 0; i < len; i++)    {
                    if(nums[i] > cur)   {
                        cur = nums[i];
                        nums[index++] = cur;
                    }
                    else
                        ++repeat;
                }
        
                return len - repeat;
            }
        };



<br/>

#### 更为精简

只用一个变量，来存储重复的数量，
如果连续的两个数值不等，就将之前重复的替换掉（根据重复的数量可以求出）。



        class Solution {
        public:
            int removeDuplicates(vector<int>& nums) {
                int repeat = 0, len = nums.size();
                for(int i = 1; i < len; i++){
                    if(nums[i] == nums[i-1]) repeat++;
                    else nums[i-repeat] = nums[i];
                }
                return len-repeat;
            }
        };