---
title: Leetcode_21  Merge Two Sorted Lists
date: 2017-04-30 17:17:00
tags: [Leetcode, 算法]
---

Leetcode_21  Merge Two Sorted Lists


<!-- more -->
<br/>


        /**
        *  Index: 21
        *  Title: Merge Two Sorted Lists
        *  Author: ltree98
        **/


<br/>


将两个有序的数组合并成一个数组。


<br/>

#### 第三方介入

最通俗易懂的，设置一个第三方，来进行存储合并。



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
            ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
                ListNode* l3 = new ListNode(0);
                ListNode* head = l3;
        
                while(l1 && l2) {
                    if(l1->val <= l2->val)  {
                        ListNode* temp = new ListNode(l1->val);
                        l3->next = temp;
                        l1 = l1->next;
                    }
                    else    {
                        ListNode* temp = new ListNode(l2->val);
                        l3->next = temp;
                        l2 = l2->next;
                    }
                    l3 = l3->next;
                }
        
                if(l1)
                    l3->next = l1;
                else
                    l3->next = l2;
        
                return head->next;
            }
        };


<br/>


#### 递归



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
            ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
                if(l1 == NULL)  return l2;
                if(l2 == NULL)  return l1;
        
                if(l1->val <= l2->val)  {
                    l1->next = mergeTwoLists(l1->next, l2);
                    return l1;
                }
                else    {
                    l2->next = mergeTwoLists(l1, l2->next);
                    return l2;
                }
            }
        };
