---
title: Leetcode_24  Swap Nodes in Pairs
date: 2017-05-08 21:45:00
tags: [Leetcode, 算法]
---

Leetcode_24  Swap Nodes in Pairs


<!-- more -->
<br/>


        /**
        *  Index: 24
        *  Title: Swap Nodes in Pairs
        *  Author: ltree98
        **/


<br/>


两个指针，一个前一个后，然后互换值，如果没有后面的就不换。

<br/>



        /**
         * Definition for singly-linked list.
         * struct ListNode {
         *     int val;
         *     ListNode *next;
         *     ListNode(int x) : val(x), next(NULL) {}
         * };
         */
        class Solution {
        public:
            ListNode* swapPairs(ListNode* head) {
                ListNode* ans = head;
                ListNode* pre = head;
        
                while(pre && pre->next) {
                    ListNode* bck = pre->next;
        
                    pre->val += bck->val;
                    bck->val = pre->val - bck->val;
                    pre->val -= bck->val;
        
                    pre = bck->next;
                }
        
                return ans;
            }
        };