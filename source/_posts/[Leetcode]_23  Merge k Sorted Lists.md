---
title: Leetcode_23  Merge k Sorted Lists
date: 2017-05-07 21:12:00
tags: [Leetcode, 算法]
---

Leetcode_23  Merge k Sorted Lists


<!-- more -->
<br/>


        /**
        *  Index: 23
        *  Title: Merge k Sorted Lists
        *  Author: ltree98
        **/


<br/>


#### 齐头并进的比较

1. 比较一轮，
2. 记录最小值及最小值的位置，
3. 创建新链加载最小值，
4. 将最小值位置的那条链后移一位，
5. 回到1开始重复

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
            ListNode* mergeKLists(vector<ListNode*>& lists) {
                ListNode* master = new ListNode(0);
                ListNode* head = master;
        
               while(true)  {
                    int min = INT_MAX;
                    int index = -1;
        
                    for(int i = 0; i < lists.size(); i++)   {
                        if(lists[i] && lists[i]->val < min) {
                            min = lists[i]->val;
                            index = i;
                        }
                    }
        
                    if(index != -1) {
                        master->next = new ListNode(min);
                        master = master->next;
                        lists[index] = lists[index]->next;
                    }
                    else
                        break;
                }
                return head->next;    
            }
        };



<br/>

#### 齐头并进的比较 & 剪枝

加了个剪枝，就是将vector中的空的链，直接移除，不再遍历。
然后，如果删除空链后只剩下一条链，那么新链直接连在这条链后面。

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
            ListNode* mergeKLists(vector<ListNode*>& lists) {
                ListNode* master = new ListNode(0);
                ListNode* head = master;
        
               while(true)  {
                    int min = INT_MAX;
                    int index = -1;
                    int needRmvIndex = -1;
                    for(int i = 0; i < lists.size(); i++)   {
                        if(lists[i])    {
                            if(lists[i]->val < min) {
                                min = lists[i]->val;
                                index = i;
                            }
                        }
                        else    {
                            needRmvIndex = i;
                        }
                    }
        
                    if(index != -1) {
                        master->next = new ListNode(min);
                        master = master->next;
                        lists[index] = lists[index]->next;
                    }
                    else
                        break;
                        
                    if(needRmvIndex != -1)  {
                        vector<ListNode*>::iterator iter = lists.begin()+needRmvIndex;
                        lists.erase(iter);
        
                        if(lists.size() == 1 && lists[0])   {
                            master->next = lists[0];
                            break;
                        }
                    }
                }
                return head->next;     
            }
        };


<br/>

#### 两两合并

不断将两条链合并，直至剩下一条链。



        /**
         * Definition for singly-linked list.
         * struct ListNode {
         *     int val;
         *     ListNode *next;
         *     ListNode(int x) : val(x), next(NULL) {}
         * };
         */
        class Solution {
        private:
            ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
                if(l1 == NULL)
                    return l2;
                if(l2 == NULL)
                    return l1;
                if(l1->val <= l2->val){
                    l1->next = mergeTwoLists(l1->next, l2);
                    return l1;
                }
                else{
                    l2->next = mergeTwoLists(l1, l2->next);
                    return l2;
                }
            }
        public:
            ListNode *mergeKLists(vector<ListNode *> &lists) {
                if(lists.empty())
                    return NULL;
                
                while(lists.size() > 1){
                    lists.push_back(mergeTwoLists(lists[0], lists[1]));
                    lists.erase(lists.begin());
                    lists.erase(lists.begin());
                }
                return lists.front();
            }
        };