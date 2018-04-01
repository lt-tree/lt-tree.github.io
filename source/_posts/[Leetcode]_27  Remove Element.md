---
title: Leetcode_27  Remove Element
date: 2017-05-15 22:17:00
tags: [Leetcode, 算法]
---

Leetcode_27  Remove Element


<!-- more -->
<br/>


        /**
        *  Index: 27
        *  Title: Remove Element
        *  Author: ltree98
        **/


<br/>


#### 记录不重复的序列号，然后往前并。



        class Solution {
        public:
            int removeElement(vector<int>& nums, int val) {
                int index = 0, len = nums.size();
                for(int i = 0; i < len ; i++) 
                    if(nums[i] != val) 
                        nums[index++] = nums[i];
                return index;       
            }
        };



<br/>

#### 从后往前遍历，如果是目标值，跟尾部的值交换（根据出现目标值的个数）


        class Solution {
        public:
            int removeElement(vector<int>& nums, int val) {
                int repeat = 0, len = nums.size() - 1;
        
                for(int i = len; i >= 0; i--)   {
                    if(nums[i] == val)  {
                        nums[i] ^= nums[len-repeat];
                        nums[len-repeat] ^= nums[i];
                        nums[i] ^= nums[len-repeat];
                        ++repeat;
                    }
                }
        
                return (len - repeat + 1);
            }
        };



