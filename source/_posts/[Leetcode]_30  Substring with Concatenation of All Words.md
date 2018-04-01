---
title: Leetcode_30  Substring with Concatenation of All Words
date: 2017-05-30 22:40:00
tags: [Leetcode, 算法]
---

Leetcode_30  Substring with Concatenation of All Words


<!-- more -->
<br/>


        /**
        *  Index: 30
        *  Title: Substring with Concatenation of All Words
        *  Author: ltree98
        **/


<br/>


题意就是求字符串s中是否有子串是全由容器words内字符串组成的。

<br/>

#### 模拟
模拟求这个问题的方法：
1.取原串一部分，然后看子串容器中是否有它，如果有往下看（跳到2）；如果没有则失败。（回到1）
2.子串容器中有这部分，看用了几次，用的次数小于等于子串容器中拥有的个数，继续往下（跳到3），否则失败。（回到1）
3.如果已经匹配完了所有子串，成功，记录序列值。（回到1）

这里要注意一下，构建比较标准映射的值，不可以全置为固定值，而是要用递增。
因为，子串可能有重复的值。



        class Solution {
        public:
            vector<int> findSubstring(string s, vector<string>& words) {
                int sLen = s.length(), wordsSize = words.size(), perWordLen = words[0].length();
                map<string, int> baseMap;
                for(int i = 0; i < wordsSize; i++)
                    ++baseMap[words[i]];
        
                vector<int> ans;
                for(int i = 0; i < sLen-wordsSize*perWordLen+1; i++)    {
                    map<string, int> comparedMap;
                    int j = 0;
                    while(j < wordsSize)    {
                        string word = s.substr(i+j*perWordLen, perWordLen);
                        if(baseMap.find(word) != baseMap.end()) {
                            ++comparedMap[word];
                            if(comparedMap[word] > baseMap[word])
                                break;
                        }
                        else    break;
                        ++j;
                    }
                    if(j == wordsSize)
                        ans.push_back(i);
                }
                return ans;
            }
        };
