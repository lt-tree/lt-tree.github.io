---
title: Leetcode_12  Integer to Roman
date: 2017-04-05 22:20:11
tags: [Leetcode, 算法]
---

Leetcode_12  Integer to Roman


<!-- more -->
<br/>


        /**
        *  Index: 12
        *  Title: Integer to Roman
        *  Author: ltree98
        **/


<br/>

罗马数字是欧洲在阿拉伯数字传入之前使用的一种数码。
罗马数字采用七个罗马字母作数字,即Ⅰ(1)、X(10)、C (100)、M (1000),V (5)、L(50)、D (500)。
记数的方法:
(1)相同的数字连写,所表示的数等于这些数字相加得到的数,如, Ⅲ = 3；
(2)小的数字在大的数字的右边,所表示的数等于这些数字相加得到的数, 如,Ⅷ = 8,Ⅻ = 12；
(3)小的数字,(限于Ⅰ、X 和 C)在大的数字的左边,所表示的数等于大数减小数得到的数,如,Ⅳ = 4,Ⅸ = 9；
(4)在一个数的上面画一条横线,表示这个数增值 1 000 倍,如 Ⅻ = 12 000 。

罗马数字的组数规则,有几条须注意掌握；
(1)基本数字Ⅰ、X 、C 中的任何一个,自身连用构成数目,或者放在大数的右边连用构成数目,都不能超过三个；放在大数的左边只能用一个。
(2)不能把基本数字 V 、L 、D 中的任何一个作为小数放在大数的左边采用相减的方法构成数目；放在大数的右边采用相加的方式构成数目,只能使用一个。
(3)V 和 X 左边的小数字只能用Ⅰ。
(4)L 和 C 左边的小数字只能用X。
(5)D 和 M 左边的小数字只能用C 。

<br/>

####  解法1

先找了找规律，
发现一个数可以拆分成下面这种形式：
2143 = 2*1000 + 1*100 + 4*10 + 3*1

对于不同阶位的 1、5、10 所对应的罗马字符不同：



        阶位  1       5       10
        个位  I       V       X
        十位  X       L       C
        百位  C       D       M
        千位  M



综上所述，
做两张映射表，
第一个，1-9所对应的罗马数模板，
第二个，阶位的1，5，10所对应的字符，
然后进行字符串查找替换。


<br/>



        class Solution {
        private:
            string numTemplate[11] = {"", "F", "FF", "FFF", "FS", "S", "SF", "SFF", "SFFF", "FT"};
            char orderTemplate[4][3] = {'I', 'V', 'X',
                                        'X', 'L', 'C',
                                        'C', 'D', 'M',
                                        'M', 'M', 'M'};
        
        
        public:
            string numByOther(int num, int order)   {
                char fO = orderTemplate[order][0];
                char sO = orderTemplate[order][1];
                char tO = orderTemplate[order][2];
        
                string temp = numTemplate[num];
                for(int i = 0; i < temp.length(); i++)  {
                    if(temp[i] == 'F')
                        temp[i] = fO;
                    else if(temp[i] == 'S')
                        temp[i] = sO;
                    else if(temp[i] == 'T')
                        temp[i] = tO;
                }
        
                return temp;
            }
        
            string intToRoman(int num) {
                string ans = "";
                int order = 0;
                while(num > 0)  {
                    int temp = num%10;
                    string sTemp = numByOther(temp, order);
                    ans = sTemp + ans;
                    num /= 10;
                    ++order;
                }
        
                return ans;
            }
        };



<br/>

#### 解法2:贪心


从大数往后依次计算能不能替换，如果能替换，并减去所替换的数的值，再继续替换到最后。

举个小例子，7
10 - X, 5 - V, 4 - IV, 1 - I
从最大的数10, 7无法用10来替换,
所以用后面的5, 5可以替换,         答案:V    数字:2(7-5)
2小于5, 小于4, 无法用5和4替换,
但可以用1来替换:   答案:VI       数字:1(2-1)
还可以用1来替换:   答案:VII  数字:0(1-1)
数字归0, 返回答案: VII

<br/>


        class Solution {
        public:
            string intToRoman(int num) {
                int values[13] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
                string valueToRoman[13] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        
                string ans = "";
                for(int i = 0; i < 13; i++) {
                    while(num >= values[i]) {
                        num -= values[i];
                        ans = ans + valueToRoman[i];
                    }
                }
        
                return ans;
            }
        };