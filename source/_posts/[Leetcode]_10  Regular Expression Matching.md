---
title: Leetcode_10  Regular Expression Matching
date: 2017-04-01 15:35:11
tags: [Leetcode, 算法]
---

Leetcode_10  Regular Expression Matching


<!-- more -->
<br/>

        /**
        *  Index: 10
        *  Title: Regular Expression Matching
        *  Author: ltree98
        **/


注意: 


        ab <==> .*      true
        .* => .. => ab  


<br/>

#### 1

<br/>

        p.empty()   &&  s.empty() == true   =>  true
                    &&  s.empty() == false  =>  false 

        s:     p:       finally:
        a   => a*   =>  s, p.substr(2) or s.substr(1), p
            => .*   =>  s, p.substr(2) or s.substr(1), p
            => a    =>  s.substr(1), p.substr(1)
            => .    =>  s.substr(1), p.substr(1)
            => else =>  false


<br/>


        class Solution {
        public:
            bool isMatch(string s, string p) {
                if( p.empty() )
                    return s.empty();
                else if( p.length() > 1 && p[1] == '*' ) {
                    if( (s[0] == p[0] or p[0] == '.') and !s.empty() )
                        return (isMatch(s, p.substr(2)) or isMatch(s.substr(1), p));
                    return isMatch(s, p.substr(2));
                }
                else{
                    if( (s[0] == p[0] or p[0] == '.') and !s.empty() )
                        return isMatch(s.substr(1), p.substr(1));
                }
                return false;
            }
        };


<br/>

#### 2.DP

<br/>


        matchArray[i][j]: matchArray[i-1][j-1] 
        p[j-1] == '*'   =>  matchArray[i][j] = matchArray[i][j-2] || (s[i-1] == p[j-2] || p[j-2] == '.') && matchArray[i-1][j];
               else     =>  matchArray[i][j] = matchArray[i-1][j-1] && (s[i-1] == p[j-1] || p[j-1] == '.');


<br/>

       
        class Solution {
        public:
            bool isMatch(string s, string p) {
                int sLen = s.length(), pLen = p.length();
                bool matchArray[1001][1001];
                matchArray[0][0] = true;
               
                for(int i = 1; i <= sLen; i++)
                    matchArray[i][0] = false;
                for(int j = 1; j <= pLen; j++)
                    matchArray[0][j] = j > 1 && p[j-1] == '*' && matchArray[0][j-2];
        
                for(int i = 1; i <= sLen; i++)  {
                    for(int j = 1; j <= pLen; j++)  {
                        if( p[j-1] == '*' )
                            matchArray[i][j] = matchArray[i][j-2] || (s[i-1] == p[j-2] || p[j-2] == '.') && matchArray[i-1][j];
                        else 
                            matchArray[i][j] = matchArray[i-1][j-1] && (s[i-1] == p[j-1] || p[j-1] == '.');
                    }
                }
        
                return matchArray[sLen][pLen];
            }
        };