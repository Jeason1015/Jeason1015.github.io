title: LeetCode刷题日记(持续更新中...)
author: 王建森
tags:
  - 算法
  - LeetCode
categories:
  - 算法
date: 2019-04-24 21:30:00
---
立志刷500道leetcode..每天更新..
# 3.给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

+ 示例 1:
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
+ 示例 2:
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
+ 示例 3:
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
  

拿到这个题目首先的想法是建立一个滑动窗口，利用两个变量来维护，之后对字符串进行遍历，一边遍历一边对滑动窗口内的所有字符进行检测是否重复，如果没有重复则窗口右侧增加一位，如果有重复，则滑动窗口左侧指针更新为窗口内重复元素的位置+1。代码如下：
```
#include <iostream>
#include <cstdlib>
#include <cstring>
using namespace std;

int main(int argc, char const *argv[])
{
	string s = "pwwkew";  //样例信息
    int l, ans;      //利用l和i维持一个滑动窗口，窗口范围是[l,i)
    l = 0;    
    ans = 0;
    string s_temp;

	for (int i = 0; i < s.length(); ++i)
	{
		s_temp = s.substr(l,i-l);    // s.substr(pos,n);  从第pos个位置开始（包括pos）,返回n个字符；
    	for(int j = 0; j < s_temp.length(); j++){    //从滑动窗口内开始遍历，如果串口内存在与正在扫描的第i个字符相同，则改变l指针
    		if( (s_temp[j] == s[i])  && (i != 0) ){
    			l = l + j + 1;                //把l指针位置改变为：在窗口内发现的重复字符的后一个。
    		}
    	}
    	if(  (i - l + 1) >= ans ){         //更新最大滑动窗口长度；
    		ans  =  (i - l + 1);
    	}
    }
};
	}
      
    cout << ans << endl;
	return 0;
}
```
提交结果时间复杂度太高 $O(n^2)$ ,后来学习了hash_map，可以利用hash—map来记录每个字符串出现的位置，这样就不需要对滑动窗口的每个元素进行遍历了，而且hash_map的复杂度是$O(1)$，所以最后的复杂度是 $O(n)$ 。代码更改如下：
```
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int res = 0, left = -1, n = s.size();
        unordered_map<int, int> m;
        for (int i = 0; i < n; ++i) {
            if (m.count(s[i]) && m[s[i]] > left) 
                left = m[s[i]];  
            m[s[i]] = i;
            res = max(res, i - left);            
        }
        return res;
    }
};
```