---
title: Leetcode_4  Median of Two Sorted Arrays
date: 2017-03-20 22:17:35
tags: [Leetcode, 算法]
---

Leetcode_4  Median of Two Sorted Arrays
简单题, 分治法

<!-- more -->
<br/>

本题是非常经典的一个题目，应该经常出现在面试题中。
给两个有序数组，求他们所有数的中位数。

<br/>

第一种方法
用merge函数，将两个vector合并起来，然后直接输出中位数...


        class Solution {
        public:
            double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
            	int allSize = nums1.size() + nums2.size();
                std::vector<int> allNums(allSize);
                merge(nums1.begin(), nums1.end(), nums2.begin(), nums2.end(), allNums.begin());
        
                allSize -= 1;
                double medianNum = 0;
                if((allSize+1) % 2 == 0){
                	medianNum = (allNums[allSize/2] + allNums[allSize/2+1]) / 2.0;
                }
                else{
                	int tempNum = int(allSize/2.0 + 0.5);
                	medianNum = allNums[tempNum];
                }
        
                return medianNum;
            }
        };



<br/>
题目建议，时间复杂度最好为 O(log(m+n))
按这个要求，用分治法应该是不错的。
这种方法，
1. 就是根据K值（一般取 K/2, 但也要注意数量，所以取 min(K/2, len) )，将 数组A 与 数组B 分别分成两部分： frontA, backA; frontB, backB;
2. 然后比较 backA前一个数 与 backB前一个数，
3. 因为两个数组分别有序，而且frontA 与 frontB所含数字数量均小于等于K；
4. 所以必然可以抛弃 frontA 与 frontB中的一部分，
5. 每次都抛弃一部分，直到找到第K个数。


        class Solution {
        private:
            int findKthNum(vector<int>::iterator A, int lenA, vector<int>::iterator B, int lenB, int K)   {
                if(lenA > lenB)
                    return findKthNum(B, lenB, A, lenA, K);
                if(lenA == 0)
                    return B[K-1];
                if(K == 1)
                    return min(A[0], B[0]);
        
                int midA = min(K/2, lenA), midB = K - midA;
                if(A[midA - 1] < B[midB - 1])
                    return findKthNum(A + midA, lenA-midA, B, lenB, K-midA);
                else if(A[midA - 1] > B[midB - 1])
                    return findKthNum(A, lenA, B + midB, lenB-midB, K-midB);
                else
                    return A[midA - 1];
            }
        
        public:
            double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
                int m = nums1.size(), n = nums2.size();
                vector<int>::iterator A = nums1.begin();
                vector<int>::iterator B = nums2.begin();
        
                int total = m + n;
                if( total & 0x1 )
                    return findKthNum(A, m, B, n, total/2+1);
                else
                    return (findKthNum(A, m, B, n, total/2) + findKthNum(A, m, B, n, total/2+1)) / 2.0;
            }
        };







