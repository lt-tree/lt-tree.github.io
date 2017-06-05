---
title: Leetcode_16  3Sum Closest
date: 2017-04-17 23:47:11
tags: [Leetcode, 算法]
---

Leetcode_16  3Sum Closest


<!-- more -->
<br/>


        /**
        *  Index: 16
        *  Title: 3Sum Closest
        *  Author: ltree98
        **/


<br/>


第15题的变种，
之前是三个数相加等于0，
这次让三个数相加等于给定的数，若相等，直接返回给定的数。
否则就一直算。

注意：
此处ans初始值设置为INT_MAX,
但是ans类型不能是INT，
因为当target是负数时，INT_MAX-target 会溢出。

<br/>


        class Solution {
        public:
            int threeSumClosest(vector<int>& nums, int target) {
                std::sort(nums.begin(), nums.end());
                long ans = INT_MAX;
        
                for(int i = 0; i < nums.size(); )   {
                    int l = i+1, h = nums.size()-1;
                    while(l < h)    {
                        int temp = nums[i] + nums[l] + nums[h];
                        if(temp == target)  {
                            return target;
                        }
                        else if(temp < target)   {
                            if(abs(ans-target) > abs(temp-target))
                                ans = temp;
                            l++;
                        }
                        else {
                            if(abs(ans-target) > abs(temp-target))
                                ans = temp;
                            h--;
                        }
                    }
        
                    i++;
                    while(i < nums.size() && nums[i] == nums[i-1])
                        i++;
                }
        
                return ans;
            }
        };


