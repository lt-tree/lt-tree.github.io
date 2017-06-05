---
title: Leetcode_18  4Sum
date: 2017-04-24 22:46:11
tags: [Leetcode, 算法]
---

Leetcode_18 4Sum


<!-- more -->
<br/>


        /**
        *  Index: 18
        *  Title: 4Sum
        *  Author: ltree98
        **/


<br/>


这道题和之前的 3Sum, 3Sum Closest 差不多。
用的同样的方法。

值得一说的就是这里的一个小剪枝。
因为进行了排序，如果发现:

- 从某个数开始连加都小于给定的目标值，那么 >整个< 就不需要查了，后面的数一定没有正确答案。
- 某个数加上最大的三个数仍小于给定的目标值，那么 >本数< 就不需要查了，怎么加都比目标值小。



<br/>


        class Solution {
        public:
            vector<vector<int>> fourSum(vector<int>& nums, int target) {
                int len = nums.size();
                if(len < 4)
                    return {};
        
                std::sort(nums.begin(), nums.end());
                vector<vector<int>> ans;
                
                for(int i = 0; i < len - 3; i++)    {
                    if(i > 0 && nums[i] == nums[i-1]) continue;
        
                    // 关键剪枝
                    if(nums[i]+nums[i+1]+nums[i+2]+nums[i+3] > target) break;
                    if(nums[i]+nums[len-3]+nums[len-2]+nums[len-1] < target) continue;
        
                    for(int j = i+1; j < len - 2; ) {
                        int l = j+1, h = len - 1, s = target-nums[i]-nums[j];
                        while(l < h)    {
                            if(nums[l] + nums[h] == s)  {
                                ans.push_back({nums[i], nums[j], nums[l], nums[h]});
                                l++, h--;           
                                while(l < h && nums[l] == nums[l-1])
                                    l++;
                                while(l < h && nums[h] == nums[h+1])
                                    h--;
                            }
                            else if(nums[l] + nums[h] < s)
                                l++;
                            else
                                h--;
                        }
        
                        j++;
                        while(j < len-2 && nums[j] == nums[j-1])    j++;
                    }
                }
                return ans;
            }
        };