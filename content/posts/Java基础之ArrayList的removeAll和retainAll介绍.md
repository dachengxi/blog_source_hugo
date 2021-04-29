---
title: Java基础之ArrayList的removeAll和retainAll介绍
date: 2019-02-08 15:24:14
categories: Java基础
---

对ArrayList中removeAll方法和retainAll方法源码、使用问题、性能问题等做个记录，之前每次看都会把这两个方法给忽略掉，这次细看的时候才发现漏掉了很大一部分的知识点。

<!--more-->

涉及到的JDK的源码版本是：Java8u192

# removeAll方法的使用示例

下面使用到两个List，list1表示主ArrayList，list2表示要删除的ArrayList。

## list1中有和list2中相同的元素

示例代码1：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);
    list2.add(2);

    System.out.println(String.format("removeAll的结果：%s", list1.removeAll(list2)));
    System.out.println(String.format("removeAll之后list1：%s", list1));
    System.out.println(String.format("removeAll之后list2：%s", list2));
}
```

运行结果：

```
removeAll的结果：true
removeAll之后list1：[]
removeAll之后list2：[1, 2]
```

示例代码2：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    list1.add(2);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);

    System.out.println(String.format("removeAll的结果：%s", list1.removeAll(list2)));
    System.out.println(String.format("removeAll之后list1：%s", list1));
    System.out.println(String.format("removeAll之后list2：%s", list2));
}
```

运行结果：

```
removeAll的结果：true
removeAll之后list1：[2]
removeAll之后list2：[1]
```

示例代码3：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    list1.add(2);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);
    list2.add(2);

    System.out.println(String.format("removeAll的结果：%s", list1.removeAll(list2)));
    System.out.println(String.format("removeAll之后list1：%s", list1));
    System.out.println(String.format("removeAll之后list2：%s", list2));
}
```

运行结果：

```
removeAll的结果：true
removeAll之后list1：[]
removeAll之后list2：[1, 2]
```

## list1中list2中没有相同元素

示例代码1：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    List<Integer> list2 = new ArrayList<>();
    list2.add(2);

    System.out.println(String.format("removeAll的结果：%s", list1.removeAll(list2)));
    System.out.println(String.format("removeAll之后list1：%s", list1));
    System.out.println(String.format("removeAll之后list2：%s", list2));
}
```

运行结果：

```
removeAll的结果：false
removeAll之后list1：[1]
removeAll之后list2：[2]
```

## 代码示例总结

可以看到，removeAll方法只有在list1有变化的时候才会返回true，如果没有任何变化则返回false。

# retainAll方法的使用示例

下面使用到两个List，list1表示主ArrayList，list2表示要保留的ArrayList。

## list1中有和list2中相同的元素

示例代码1：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);
    list2.add(2);

    System.out.println(String.format("retainAll的结果：%s", list1.retainAll(list2)));
    System.out.println(String.format("retainAll之后list1：%s", list1));
    System.out.println(String.format("retainAll之后list2：%s", list2));
}
```

运行结果：

```
retainAll的结果：false
retainAll之后list1：[1]
retainAll之后list2：[1, 2]
```

示例代码2：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    list1.add(2);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);

    System.out.println(String.format("retainAll的结果：%s", list1.retainAll(list2)));
    System.out.println(String.format("retainAll之后list1：%s", list1));
    System.out.println(String.format("retainAll之后list2：%s", list2));
}
```

运行结果：

```
retainAll的结果：true
retainAll之后list1：[1]
retainAll之后list2：[1]
```

示例代码3：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    list1.add(2);
    List<Integer> list2 = new ArrayList<>();
    list2.add(1);
    list2.add(2);

    System.out.println(String.format("retainAll的结果：%s", list1.retainAll(list2)));
    System.out.println(String.format("retainAll之后list1：%s", list1));
    System.out.println(String.format("retainAll之后list2：%s", list2));
}
```

运行结果：

```
retainAll的结果：false
retainAll之后list1：[1, 2]
retainAll之后list2：[1, 2]
```



## list1中list2中没有相同元素

代码示例1：

```
public static void main(String[] args) {
    List<Integer> list1 = new ArrayList<>();
    list1.add(1);
    List<Integer> list2 = new ArrayList<>();
    list2.add(2);

    System.out.println(String.format("retainAll的结果：%s", list1.retainAll(list2)));
    System.out.println(String.format("retainAll之后list1：%s", list1));
    System.out.println(String.format("retainAll之后list2：%s", list2));
}
```

运行结果：

```
retainAll的结果：true
retainAll之后list1：[]
retainAll之后list2：[2]
```

## 代码示例总结

retainAll方法在主List没有变化的时候返回false，有变化的时候返回true。没变化包含了主List的所有元素全在要保留的List里面，也包括两个List全部相等的情况。

# 源码分析

>Java8u192

```
/**
 * 从ArrayList中删除给定的集合中的所有元素
 * 如果从ArrayList中删除集合中某些或全部元素，则返回true
 * 如果ArrayList中和集合中没有任何相同元素，则返回false
 *
 * 如果是对象的话，需要重写equals和hashCode方法，底层实现的时候是通过indexOf方法
 * 进行元素查找，indexOf方法中使用Object.equals方法来比较是否相等。
 */
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c);
    // 批量移除
    return batchRemove(c, false);
}

/**
 * ArrayList中只保留指定集合中的元素
 * 如果ArrayList中保留了集合中某些或全部元素，返回true
 * 如果ArrayList中和集合中没有任何相同元素，返回true
 *
 * 如果ArrayList中和集合中元素完全相同，返回false
 */
public boolean retainAll(Collection<?> c) {
    Objects.requireNonNull(c);
    // 批量移除
    return batchRemove(c, true);
}

/**
 * 批量移除指定的集合中的元素或者只保留指定集合中的元素
 * complement=false，将指定集合中的元素移除掉
 * complement=true，保留指定集合中的元素，其他的都移除掉
 *
 * 根据该方法的实现，可以知道removeAll和retainAll两个方法的返回值含义。
 * removeAll的返回如果为true表示：ArrayList删除了集合中的某些或全部元素
 * removeAll的返回如果为false表示：ArrayList和集合中没有任何相同元素
 *
 * retainAll的返回如果为true表示：
 * 1. ArrayList只保留了集合中的某些或全部元素元素
 * 2. ArrayList和集合中没有任何相同元素
 * retainAll的返回如果为false表示：ArrayList中和集合中的元素完全相同
 *
 * 基于此方法的操作时间复杂度为O(m * n)，数据量大的时候性能会变差，
 * 可以根据实际情况选择使用Set相关实现
 */
private boolean batchRemove(Collection<?> c, boolean complement) {
    // ArrayList中存储的元素
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        // 遍历数组
        for (; r < size; r++)
            /**
             * 这里分开两部分讲，第一种是complement=false，将指定集合中的元素移除掉；
             * 第二种是complement=true，保留指定集合中的元素，其他的都移除掉。
             *
             * 1. complement=false，将指定集合中的元素从ArrayList中移除掉，也就是
             * 如果ArrayList中包含该元素，就需要移除掉，反过来看就是如果ArrayList中
             * 的元素不在集合中，就保留下来。如果ArrayList中的元素在集合中存在，则
             * c.contains(elementData[r])为true，不做操作（其实这个元素是需要移除的），循环继续，r自增。
             * 如果ArrayList中的元素在集合中不存在，则c.contains(elementData[r])为false，此时进入if语句中，
             * 将当前这个元素放到这个元素之前的需要移除的元素位置上进行覆盖，这样就保留了ArrayList中的元素，
             * 将ArrayList中其他的元素都移除掉。w自增，r自增。
             * 这样循环结束后，r = size，w = size - 集合中和ArrayList匹配到的元素的个数
             *
             * 2. complement=true，保留指定集合中的元素，其他的都移除掉，也就是ArrayList中
             * 如果包含该元素就保留下来。如果ArrayList中的元素在集合中不存在，则
             * c.contains(elementData[r])为false，不做操作（其实这个元素是需要移除的），循环继续，r自增。
             * 如果ArrayList中的元素在集合中存在，则c.contains(elementData[r])为true，此时进入if语句中，
             * 将当前这个元素放到这个元素之前的需要移除的元素位置上进行覆盖，这样就保留了集合中的元素，
             * 将ArrayList中其他的元素都移除掉，w自增，r自增。
             * 这样循环结束后，r = size，w = 集合中和ArrayList匹配到的元素的个数
             *
             * 上面两种都循环完后，w之前的都是要保留的元素
             */
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // Preserve behavioral compatibility with AbstractCollection,
        // even if c.contains() throws.
        // 这里是处理抛出异常的场景，抛出异常的时候，还没循环完，r != size，
        // w之前是要保留的元素，但是只是一部分，需要将这部分元素保留下来，
        // 还要将剩余的元素复制到w之后
        if (r != size) {
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            // w = w + r之后的长度
            w += size - r;
        }
        // w不等于size，表示原ArrayList发生了改变或者w=0没有任何变化
        if (w != size) {
            // clear to let GC do its work
            // w之后的元素都没用了，需要置为null
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            // w为最后ArrayList真正的大小
            size = w;
            /**
             * modified = true，表示原ArrayList发生了改变：
             * 如果是removeAll方法调用
             * 1. 可能表示ArrayList删除了集合中的某些或全部元素
             *
             * 如果是retainAll方法调用
             * 1. 可能表示ArrayList只保留了集合中的某些或全部元素
             * 2. 可能表示ArrayList和集合中没有任何相同元素
             */
            modified = true;
        }
    }
    return modified;
}
```

# 性能注意事项

从源码中可以看到会进行for循环，并且for循环中调用了contains方法，而ArrayList的contains方法使用的是indexOf方法，indexOf方法中也是使用遍历进行元素查找。时间复杂度是O(m * n)。如果涉及到两个比较大的List进行操作，性能会很差。可以根据实际情况使用Set相关实现来进行操作。

# 使用注意事项

代码中使用了contains方法，contains方法使用indexOf来实现，indexOf方法中比较两个元素是使用Object.equals方法进行的，所以List中如果是对象的话，记得重写equals方法和hashCode方法，否则会产生不正确的结果。

# 参考内容

