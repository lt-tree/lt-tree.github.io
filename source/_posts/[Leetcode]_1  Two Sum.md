---
title: Leetcode_1  Two Sum
date: 2017-03-14 22:45:35
tags: [Leetcode, 算法]
---

Leetcode_1  Two Sum
简单题, 映射数组

<!-- more -->
<br/>

感觉，最近算法思维在退步。
是时候练一练了，不为进步，就为了恢复并维持之前的状态。

<br/>

第一道题，
给一个数组与一个和，
求数组内哪两个数相加可以等于那个和，
同一个数无法用两次，输出两个数的次序。

第一个方法，喜（fei）闻（chang）乐（low）见（bi）的穷举遍历。
时间复杂度O（n^2），或许是因为第一题，竟然没有超时...

<br/>

第二个方法，
构造一个映射数组，默认置为-1，
给一个数（比如7），
立马知道它所对应的的另一个数（如果和是9，那么另一个数肯定是2），
在那个数下存储本数的次序（arr[2] = 7的次序），
每次读数组内的数，判断它在映射数组内的值（比如读到2，发现映射数组中值非-1），
那么求可以得出答案。
此方法复杂度为O(n)

<br/>


		/**
		 *  Index: 	1
		 *  Title: 	Two Sum
		 *	Author:	ltree98
		 **/

		// O(n^2)
		class Solution {
		public:
			vector<int> twoSum(vector<int>& nums, int target) {
				vector<int> ans;
				for (int i = 0; i < nums.size(); i++) {
					for (int j = i + 1; j < nums.size(); j++) {
						if (nums[i] + nums[j] == target) {
							ans.push_back(i);
							ans.push_back(j);
							return ans;
						}
					}
				}
			}
		};

		// O(n)
		class Solution {
		public:
			vector<int> twoSum(vector<int>& nums, int target) {
				vector<int> ans;
				int arr[100001];
				memset(arr, -1, sizeof(arr));
				for (int i = 0; i < nums.size(); i++) {
					if( arr[nums[i]] != -1 )	{
						ans.push_back(arr[nums[i]]);
						ans.push_back(i);
						return ans;
					}

					int temp = nums[i] - target;
					if(temp < 0){
						temp = -temp;
					}
		
					arr[temp] = i;
				}
			}
		};



















