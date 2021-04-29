---
title: 算法之串模式匹配KMP算法学习
date: 2020-03-21 20:41:58
categories: 数据结构与算法
---

 串的模式匹配KMP算法重新学习。

<!--more-->

# 串的模式匹配

假设有两个串：目标串target和模式串pattern，在目标串中查找与模式串相等的一个子串并确定该字串的位置的操作叫串的模式匹配（Pattern Matching）

# KMP算法

BF（Brute-Force）算法的目标串存在回溯，实际上目标串的回溯是不必要的，KMP算法就是一种利用前一次匹配结果来去除回溯的模式匹配算法，改进了BF算法，目标串不回溯。KMP算法描述如下：

已知目标串target="$t_0t_1...t_{n-1}$" ，模式串pattern="$p_0p_1...p_{m-1}$"，0 < m <= n，KMP算法每次匹配依次比较$t_i$与$p_j$（0 <= i < n, 0 <= j < m）。

1. 如果$t_i = p_j$，则继续比较$t_{i+1}$和$p_{j+1}$，直到匹配完："$t_{i-m+1}...t_i$"="$p_0...p_{m-1}$"，表示匹配成功，返回模式串在目标串中的开始索引i-m
2. 如果$t_i != p_j$，表示"$t_{i-j}...t_i$"和"$p_0...p_j$"匹配失败，目标串不回溯，$t_i$不动，下次匹配将$t_i$和模式串中的$p_k$（0 <= k < j）进行比较。每个$p_j$对应的k都不相同，需要求每个$p_j$对应的k。

$p_j$对应的k的取值是：$p_j$前面的串（$p_0...p_{j-1}$）所包含的最长的相等的前缀子串和后缀子串的长度：

- 需要找的时候$p_j$前面的串：$p_0...p_{j-1}$
- 并且是$p_0...p_{j-1}$串中的相等的前缀子串和后缀子串
- 并且是最长的前后缀子串

每个$p_j$对应的k可以组成一个数组，称为next数组。

如果$t_i != p_j$，则有"$t_{i-j}...t_{i-1}$"="$p_0...p_{j-1}$"，假设"$p_0...p_{j-1}$"串中存在相同的前缀子串"$p_0...p_{k-1}$"和后缀子串"$p_{j-k}...p_{j-1}$"，则："$p_0...p_{k-1}$"="$p_{j-k}...p_{j-1}$"="$t_{i-k}...t_{i-1}$"，下次匹配，目标串位置i不变，模式串位置从k继续和i进行比较，也就是$p_k$和$t_i$继续比较。

# next数组

next数组定义next[j]：
$$
next[j] = 
\begin{cases}
-1 & 当j=0时 
\\\\
k & 当0<=k<j时且使"p_0...p_{k-1}"="p_{j-k...p_{j-1}}"的最大整数
\end{cases}
$$


求next[j]数组，也就是求j前面的串中最长的相同前缀和后缀子串的长度。求出了next[j]之后，也可以利用已经得到的结果来求next[j+1]。

1. 约定next[0]=-1，有next[1]=0
2. 假设模式串当前索引为j（0 <= j < m），有next[j]=k，表示"$p_0...p_{j-1}$"串中存在长度为k的相同前缀和后缀子串，就是："$p_0...p_{k-1}$"="$p_{j-k}...p_{j-1}$"，0 <= k < j且k取最大值。

如果要求next[j+1]的值，也就是求"$p_0...p_{j-1},p_j$"串中最长的相同前缀和后缀子串的长度，现在已知"$p_0...p_{k-1}$"="$p_{j-k}...p_{j-1}$"，接下来需要比较$p_k$和$p_j$是否相等：

1. 如果$p_k ==p_j$，则"$p_0...p_k$"="$p_{j-k}...p{j}$"，长度是k+1，也就是next[j+1] = k + 1 = next[j] + 1。
2. 如果$p_k != p_j$，也就是在"$p_0...p_j$"中找不到比k更长的相同前缀和后缀子串了，此时$p_k != p_j$，而k是属于"$p_0...p_{j-1}$"串的最长前缀和后缀子串长度，k肯定不能用来表示"$p_0...p_j$"串的最长长度。此时需要在"$p_0...p_j$"串中找一个比k小，但是是"$p_0...p_j$"串的次长前缀和后缀串的长度。也即是找"$p_0...p_{k-1}$"中最长前缀和后缀的长度，也就是k索引处的next数组中对应的值：next[k]，让k=next[k]后再继续比较$p_j$和$p_k$，寻找相同的前后缀子串。

对于上面第2步解释如下：

在串"$p_0...p_{k-1},p_k,p_{j-k}...p_{j-1}$"中有长度为k的最长相同前缀和后缀子串，也就是next[j]=k，前缀后缀串："$p_0...p_{k-1}$"="$p_{j-k}...p_{j-1}$"，如果要找次长的相同前缀和后缀串，则只能在现在的前缀中找，前缀是："$p_0...p_{k-1}$"，而这个串的最长前缀和后缀子串其实已经计算出来了是next[k]，找到了这个next[k]之后，对于"$p_0...p_{k-1}$"串的最长前缀后缀是："$p_0...p_{next[k]-1}$"="$p_{k-next[k]}...p_{k-1}$"，此时原来前缀的最长前缀和后缀，同原来后缀的最长前缀和后缀都是相等的，也就是原来前缀的最长前缀和原来后缀的最长后缀相等，也就是原来串的次长前缀和后缀相等。

# 代码示例

```java
public class KMPString {

    public static void main(String[] args) {
        String pattern = "ssfass";
        String target = "asdfsaasfssfasseerer";

        int indexOfTarget = kmpString(target, pattern);
        System.out.println("index of target: " + indexOfTarget);
    }

    private static int kmpString(String target, String pattern) {
        int i = 0;
        int j = 0;

        Integer[] next = getNext(pattern);

        while (i < target.length() && j < pattern.length()) {
            if (j == -1 || target.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            } else {
                j = next[j];
            }
        }

        if (j == pattern.length()) {
            return i - pattern.length();
        }
        return -1;
    }

    private static Integer[] getNext(String pattern) {
        Integer[] next = new Integer[pattern.length()];
        next[0] = -1;

        int j = 0;
        int k = -1;
        while (j < pattern.length() - 1) {
            if (k == -1 || pattern.charAt(j) == pattern.charAt(k)) {
                j++;
                k++;
                next[j] = k;
            } else {
                k = next[k];
            }
        }

        return next;
    }
}
```

KMP算法不需要回溯目标串，每次失配后目标串索引不变，模式串移动的索引需要计算，也就是next数组需要计算。

next数组是对应的值是：模式串每个索引处之前的串的最长相等前缀子串和后缀子串的长度。

# KMP算法分析

- 最好情况下，第一次匹配成功，比较次数为模式串长度m，时间复杂度为O(m)
- 最坏情况下，时间复杂度为$O(n + m)$

# 参考内容

- 《数据结构（Java版）》