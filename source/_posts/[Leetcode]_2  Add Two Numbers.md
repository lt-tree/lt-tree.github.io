---
title: Leetcode_2  Add Two Numbers
date: 2017-03-15 22:13:35
tags: [Leetcode, 算法]
---

Leetcode_2  Add Two Numbers
简单题

<!-- more -->
<br/>


很基础..
合并l1 和 l2


		class Solution {
		public:
		    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {      
    		    ListNode* lnTemp = new ListNode(0);
        		ListNode* ori = lnTemp;
    		    int op = 0;

		        while( l1 || l2 || op )   {
    		        int sum = op;
        		    if(l1)  {
            		    sum += l1->val;
            		    l1 = l1->next;
        		    }
        		    if(l2)  {
            		    sum += l2->val;
                		l2 = l2->next;
            		}

		            op = sum/10;
    		        lnTemp->next = new ListNode(sum%10);
        		    lnTemp = lnTemp->next;
        		}
        		return ori->next;
    		}
		};


<br/>
用更少的空间，
一个记录初始地址结构体，一个进位值，一个补足进位结构体。
将所有的数都加在l1上，如果l2比l1长 => l1->next = l2->next 将l2后半部分并到l1



		class Solution {
		public:
    		ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
    		    ListNode *ori = l1;
   		     int op = 0;
		
   		     while (true) {
   		         l1->val += (l2->val + op);
    		        op = l1->val/10;
   		         l1->val %= 10;

   		         if (!l1->next) {
   		             l1->next = l2->next;
  		              break;
       		     }
            		else if (!l2->next)
             		   break;

       		     l1 = l1->next;
       		     l2 = l2->next;
      		}
        
       		while (l1->next && op) {
      				l1 = l1->next;
        		    op = (l1->val += op)/10;
        		    l1->val %= 10;
        		}

        		if (op)
            		l1->next = new ListNode(op);

        		return ori;
    		}
		};











































