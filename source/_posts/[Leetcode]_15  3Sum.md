---
title: Leetcode_15  3Sum
date: 2017-04-17 23:37:11
tags: [Leetcode, 算法]
---

Leetcode_15  3Sum


<!-- more -->
<br/>


        /**
        *  Index: 15
        *  Title: 3Sum
        *  Author: ltree98
        **/


<br/>


暴力肯定TLE，不用多想。

先对所有数据进行排序，
然后从第一个数开始，取一个数，计算这个数后面是否有两个数加这个数等于0。
最关键就是:

1. 排序(此方法的基点)
2. 确定一组答案后过滤前后同值的数(过滤同样的答案, 且不影响其他不同的答案)

<br/>


        class Solution {
        public:
            vector<vector<int>> threeSum(vector<int>& nums) {
                if(nums.size() < 3)
                    return {};
        
                std::sort(nums.begin(), nums.end());
                vector<vector<int>> ans;
                
                for(int i = 0; i < nums.size(); )   {
                    int l = i+1, h = nums.size()-1, s = -nums[i];
                    while(l < h)    {
                        if(nums[l] + nums[h] == s)  {
                            ans.push_back({nums[i], nums[l], nums[h]});
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
        
                    i++;
                    while(i < nums.size() && nums[i] == nums[i-1])
                        i++;
                }
        
                return ans;
            }
        };

