---
title: Spring解决循环依赖的过程总结
date: 2020-03-22 23:25:31
categories: Spring
tags:
- Spring
---

简单将Spring是怎么解决循环依赖做一下总结，具体的源码实现网上有很多文章，这里不做具体分析。

<!-- more -->

Spring在初始化Bean的时候，会先初始化当前Bean所依赖的Bean，如果两个Bean互相依赖，就产生了循环依赖，Spring针对循环依赖的办法是：提前曝光加上三个缓存singletonObjects、earlySingletonObjects、singletonFactories。

假设当前Bean是A，A依赖的Bean是B，B又依赖A。

提前曝光的意思就是，当前Bean A实例化完，还没有初始化完就先把当前Bean曝光出去，在B初始化需要依赖A的时候，就先拿到提前曝光的A，这样就可以继续将B初始化完成，然后返回A继续进行初始化。

上面说的三个缓存的使用如下：

1. 初始化Bean A，此时三个缓存中都是空，singletonObjects: [null], earlySingletonObjects: [], singletonFactories: [null]
2. 实例化Bean A，将A放入到singletonFactories中，此时singletonObjects: [null], earlySingletonObjects: [], singletonFactories: [A]。
3. 实例化A之后，需要注入依赖的属性，发现A依赖B，开始初始化Bean B，此时singletonObjects: [null], earlySingletonObjects: [], singletonFactories: [A]
4. 实例化Bean B，将B放入到singletonFactories中，此时singletonObjects: [null], earlySingletonObjects: [], singletonFactories: [A, B]
5. 实例化B之后，需要注入依赖的属性，发现B依赖A，此时需要初始化A，这时候初始化A会去singletonFactories查找一下，发现存在A，直接获取A并放入earlySingletonObjects中，然后从singletonFactories删除，此时singletonObjects: [null], earlySingletonObjects: [A], singletonFactories: [B]
6. 此时B就拿到了上一步在singletonFactories中存在的A，B依赖注入完成，此时singletonObjects: [null], earlySingletonObjects: [A], singletonFactories: [B]
7. B依赖注入完成后继续初始化完成，会将B从earlySingletonObjects和singletonFactories中删除，添加进singletonObjects中，此时singletonObjects: [B], earlySingletonObjects: [A], singletonFactories: [null]
8. 接下来继续进行A的依赖注入，此时B已经初始化完成了，直接将B注入给A，然后A继续下面的初始化直到A完成，此时singletonObjects: [B], earlySingletonObjects: [A], singletonFactories: [null]
9. A初始化完成之后，会从earlySingletonObjects和singletonFactories中删除，添加进singletonObjects中，此时singletonObjects: [A, B], earlySingletonObjects: [null], singletonFactories: [null]
10. 这样就完成了循环依赖的解决。

循环依赖解决只针对单例Bean。