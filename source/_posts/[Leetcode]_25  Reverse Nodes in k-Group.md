---
title: Leetcode_25  Reverse Nodes in k-Group
date: 2017-05-10 23:00:00
tags: [Leetcode, 算法]
---

Leetcode_25  Reverse Nodes in k-Group


<!-- more -->
<br/>


        /**
        *  Index: 25
        *  Title: Reverse Nodes in k-Group
        *  Author: ltree98
        **/


<br/>


题目要求，将一组链表根据给定的k值，进行部分逆序。
而且，最后不足k的部分，要按照原来的顺序。


#### 1

以[1,2,3,4,5] k=3 为例

解题方法：


            pre     cur     nxt
        1.  0   -   1   -   2   -   3   -   4   -   5

            pre     nxt     cur     
        2.  0   -   2   -   1   -   3   -   4   -   5


            pre             cur     nxt
        3.  0   -   2   -   1   -   3   -   4   -   5

            pre     nxt             cur
        4.  0   -   3   -   2   -   1   -   4   -   5


其实，步骤1、2 与 步骤3、4 是一样的，不断重复这些步骤。
每次进行一次步骤，计数一次，直到到k-1；则pre移到cur的位置，继续重复以上步骤。

最后，到后面没有后续值后，如果计数器不到k-1，则再把后面的值翻过来一遍。


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
            ListNode* reverseKGroup(ListNode* head, int k) {
                if(head == NULL || k == 1)
                    return head;
        
                ListNode *ori = new ListNode(0);
                ori->next = head;
                ListNode *cur, *nxt, *pre = ori;
        
                int counter;
                while(true) {
                    counter = k;
                    cur = pre->next;
                    nxt = cur->next;
        
                    while(cur->next and counter-- > 1)  {
                        cur->next = nxt->next;
                        nxt->next = pre->next;
                        pre->next = nxt;
                        nxt = cur->next;
                    }
        
                    if(cur->next == NULL)
                        break;
                    pre = cur;
                }
        
                if(counter > 1) {
                    counter = k - counter;
                    cur = pre->next;
                    nxt = cur->next;
                    
                    while(counter--)    {
                        cur->next = nxt->next;
                        nxt->next = pre->next;
                        pre->next = nxt;
                        nxt = cur->next;
                    }
                }
        
                return ori->next;
            }
        };



<br/>

#### 2

这个方法是我看热门解题里的，通过遍历一遍链表，就不用把最后反过来的再翻回去了。
但是结果是缩短了代码行数。



        class Solution {
        public:
            ListNode *reverseKGroup(ListNode *head, int k) {
                if(head == NULL || k == 1) 
                    return head;
        
                int num = 0;
                ListNode *ori = new ListNode(-1);
                ori->next = head;
                ListNode *cur = ori, *nxt, *pre = ori;
                
                while(cur = cur->next) 
                    num++;
        
                while(num >= k) {
                    cur = pre->next;
                    nxt = cur->next;
                    for(int i = 1; i < k ; ++i) {
                        cur->next = nxt->next;
                        nxt->next = pre->next;
                        pre->next = nxt;
                        nxt = cur->next;
                    }
                    pre = cur;
                    num -= k;
                }
                return ori->next;
            }
        };