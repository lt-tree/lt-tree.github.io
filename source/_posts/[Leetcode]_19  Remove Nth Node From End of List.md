---
title: Leetcode_19  Remove Nth Node From End of List
date: 2017-04-24 22:50:11
tags: [Leetcode, 算法]
---

Leetcode_19  Remove Nth Node From End of List


<!-- more -->
<br/>


        /**
        *  Index: 19
        *  Title: Remove Nth Node From End of List
        *  Author: ltree98
        **/


<br/>


这道题关键在于一遍过。
我想法就是两个指针，相距n个位置，如果前面的指针到底了，那么后面的就是需要被排除的东西。
然后在将一些边界条件判断一下。



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
            ListNode* removeNthFromEnd(ListNode* head, int n) {
                ListNode* slow = head;
                ListNode* fast = head;
                while(n > 0)    {
                    if(fast)    {
                        fast = fast->next;
                        n--;                
                    }
                    else
                        return NULL;
                }
        
                if(fast == NULL)    {
                    slow = slow->next;
                    return slow;
                }
        
                while(fast->next) {
                    fast = fast->next;
                    slow = slow->next;
                }
            
                ListNode* temp = slow->next;
                slow->next = temp->next;
                temp = NULL;
                
                return head;
            }
        };