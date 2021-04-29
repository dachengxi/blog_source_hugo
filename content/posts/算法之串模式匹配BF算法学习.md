---
title: 算法之串模式匹配BF算法学习
date: 2020-03-21 14:53:45
categories: 数据结构与算法
---

 串的模式匹配BF算法重新学习。

<!--more-->

# 串的模式匹配

假设有两个串：目标串target和模式串pattern，在目标串中查找与模式串相等的一个子串并确定该字串的位置的操作叫串的模式匹配（Pattern Matching）

# BF算法

BF（Brute-Force）算法是一种串的模式匹配算法，算法描述如下：

已知目标串target="$t_0t_1...t_{n-1}$" ，模式串pattern="$p_0p_1...p_{m-1}$"，0 < m <= n，BF算法将目标串中的每个长度为m的字串"$t_0...t_{m-1}$"，"$t_1...t_m$"，...，"$t_{n-m}...t_{n-1}$"依次与模式串进行匹配操作，匹配成功则返回目标串中匹配到的开始索引。

BF算法中子串的匹配过程是将目标串中的字符$t_i$（0 <= i < n）与模式串中字符$p_j$（0 <= j < m）进行比较：

1. 如果$t_i = p_j$，继续比较$t_{i+1}$和$p_{j+1}$，直到j=m-1，则表示"$t_{i-m+1}...t_i$"和"$p_0...p_{m-1}$"匹配成功，返回模式串在目标串中匹配到的字串序号：i-m
2. 如果$t_i != p_j$，表示"$t_{i-j}...t_i...t_{i-j+m-1}$"与"$p_0...p_j...p_{m-1}$"匹配失败，目标串下一个匹配字串是"$t_{i-j+1}...t_{i-j+m}$"，继续比较$t_{i-j+1}$和$p_0$，此时目标串回溯，从$t_i$退回到$t_{i-j+1}$

# 代码示例

```java
public class BFString {

    public static void main(String[] args) {
        String pattern = "ssfass";
        String target = "asdfsaasfssfasseerer";

        int indexOfTarget = bfString(target, pattern);
        System.out.println("index of target: " + indexOfTarget);
    }

    private static int bfString(String target, String pattern) {
        int i = 0;
        int j = 0;

        while (i < target.length() && j < pattern.length()) {
            if (target.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            } else {
                i = i - j + 1;
                j = 0;
            }
        }

        if (j == pattern.length()) {
            return i - pattern.length();
        }

        return -1;
    }
}
```

# BF算法分析

BF算法简单易于理解，但是效率不高：

- 最好情况下，第一次匹配成功，比较次数为模式串长度m，时间复杂度为O(m)
- 最坏情况下，每次匹配比较到模式串最后一个字符，并且比较了目标串中所有长度为m的子串，时间复杂度为$O(n \times m)$

BF算法是一种带回溯的模式匹配算法，每次匹配没有利用前一次匹配的比较结果，算法中存在较多的重复比较，算法效率较低。更好地算法有：KMP算法。

# 参考内容

- 《数据结构（Java版）》