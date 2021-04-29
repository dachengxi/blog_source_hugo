---
title: 算法之TimSort学习
date: 2020-03-02 16:35:06
categories: 数据结构与算法
---

 Java源码中关于TimeSort的学习。

<!--more-->

基于Java8u192源码，学习TimSort。

# TimSort的思想

TimSort本质上利用了二分插入排序和归并排序，可认为是归并排序的优化。在实际场景下，待排序序列中有很多的部分是已经排序好的（升序或者降序），TimSort利用该特点进行改进归并排序。

待排序序列中的那些已经排好序的部分称为一个run，可认为是一个“分区“，TimSort在进行排序的时候，会使用二分插入排序将一些较小的run扩大成一些较大的run，然后再使用归并排序将这些不同的run进行合并排序，直到最后排序完成。

# 流程

TimSort大概流程是：

1. 如果元素个数较少，默认32，则使用二分插入排序字节进行排序。
2. 根据数组元素个数，选择一个合适的run的长度：minRun。
3. 分析数组，将数组划分为多个run，如果run比较小的话，则使用二分插入排序扩充run
4. 使用归并排序合并run，直到合并、排序完成。

## 选择合适的run长度

选择合适的run长度的原因是，使run的大小保持一个均衡，避免让一个很长的run和一个很短的run进行合并，这样操作的性价比比较低。

## 合并run

栈顶的run需要保持一定的规则，也是为了保证run之间的平衡。假设栈顶的三个run为C、BA则需要满足如下规则：

- A > B + C
- B > C

如果不满足的话，需要进行合并，A和B或者B和C进行合并，直到满足上面条件。

## 扩充run

在进行数组分析，划分run的时候，如果run较小则会将run进行扩充，扩充run的时候使用二分插入排序进行扩充。

## Galloping mode

在合并两个run的时候，会使用Galloping mode来减少两个run中比较排序的元素。大概的原理是如下：

- 假设有run1和run2两个run，先找run2的首元素在run1中插入的位置，这样run1中左边的元素就不需要参与排序了
- 然后中run1的最后一个元素在run2中的插入位置，这样run2中后面一部分的元素都不需要参与排序了
- 最后参与排序的只剩下run1后面部分的元素和run2前面部分的元素。

# 源码

源码可以参考Github中的源码解析：Java8u192SourceCode，详细的说明可以看参考内容中的文章，说的比较详细。


# 参考内容

- [https://sikasjc.github.io/2018/07/25/timsort/](https://sikasjc.github.io/2018/07/25/timsort/)
- [https://www.jianshu.com/p/892ebd063ad9](https://www.jianshu.com/p/892ebd063ad9)
- [https://blog.csdn.net/sinat_35678407/article/details/82974174](https://blog.csdn.net/sinat_35678407/article/details/82974174)