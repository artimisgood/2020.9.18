# 2020.9.18————最长回文子串
## 题目
给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：

输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
示例 2：

输入: "cbbd"
输出: "bb"

## 解题
### 语言
java
### 思路
对于一个子串而言，如果它是回文串，并且长度大于 22，那么将它首尾的两个字母去除之后，它仍然是个回文串。例如对于字符串 “ababa”，如果我们已经知道 “bab” 是回文串，那么 “ababa” 一定是回文串，这是因为它的首尾两个字母都是“a”。

根据这样的思路，我们就可以用动态规划的方法解决本题。我们用 P(i,j)P(i,j) 表示字符串 ss 的第 ii 到 jj 个字母组成的串（下文表示成 s[i:j]s[i:j]）是否为回文串：

 

这里的「其它情况」包含两种可能性：
![](https://github.com/artimisgood/2020.9.18/blob/master/1.png?raw=true)

s[i, j]s[i,j] 本身不是一个回文串；

i > ji>j，此时 s[i, j]s[i,j] 本身不合法。

那么我们就可以写出动态规划的状态转移方程：
![](https://github.com/artimisgood/2020.9.18/blob/master/2.png?raw=true)


也就是说，只有 s[i+1:j-1]s[i+1:j−1] 是回文串，并且 ss 的第 ii 和 jj 个字母相同时，s[i:j]s[i:j] 才会是回文串。

上文的所有讨论是建立在子串长度大于 22 的前提之上的，我们还需要考虑动态规划中的边界条件，即子串的长度为 11 或 22。对于长度为 11 的子串，它显然是个回文串；对于长度为 22 的子串，只要它的两个字母相同，它就是一个回文串。因此我们就可以写出动态规划的边界条件：

![](https://github.com/artimisgood/2020.9.18/blob/master/3.png?raw=true)
 

根据这个思路，我们就可以完成动态规划了，最终的答案即为所有 P(i, j) = \text{true}P(i,j)=true 中 j-i+1j−i+1（即子串长度）的最大值。注意：在状态转移方程中，我们是从长度较短的字符串向长度较长的字符串进行转移的，因此一定要注意动态规划的循环顺序。

#### 具体思路和图片看这里
[https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zui-chang-hui-wen-zi-chuan-by-leetcode-solution/](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zui-chang-hui-wen-zi-chuan-by-leetcode-solution/)


### 代码
```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        //特判
        if(len<2){
            return s;
        }
        //初始化最大长度和起始位置
        int maxLen = 1;
        int begin = 0;

        //1.状态定义
        //dp[i][j]表示s[i…j]是否是回文串

        //2.初始化
        boolean[][] dp = new boolean[len][len];
        for(int i = 0;i<len;i++){//将动态规划的中间分割线都转换为true
            dp[i][i]=true;
        }

        char[] chars = s.toCharArray();
        //3.状态转移
        //注意：先填左下角
        //填表规则：先一列一列的填写，在一行一行的填写，保证左下方的单元格先进行计算
        for(int j=1;j<len;j++){
            for(int i=0;i<j;i++){
                //头尾字符不相等，不是回文串
                if(chars[i]!=chars[j]){
                    dp[i][j]=false;
                }else{
                    //相等的情况下，考虑头尾去掉以后没有字符剩余，或者剩下一个一定是回文串
                    if(j-i<3){
                        dp[i][j]=true;
                    }else{
                        //状态转移
                        dp[i][j]=dp[i+1][j-1];
                    }
                }
                //只要dp[i]][j]==true成立，表示是s[i…j]是回文串
                //此时更新记录回文长度和起始位置
                if(dp[i][j]&&j-i+1>maxLen){
                    maxLen = j-i+1;
                    begin = i;
                }
            }
        }
        //4.返回值
        return s.substring(begin,begin+maxLen);
    }
}
```
