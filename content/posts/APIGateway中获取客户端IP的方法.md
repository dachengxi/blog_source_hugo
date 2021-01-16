---
title: APIGateway中获取客户端IP的方法
date: 2020-04-12 21:35:24
categories: 
- APIGateway
tags:
- APIGateway
---

在使用ServletRequest获取客户端ip的时候，不仅仅只使用getRemoteHost来获取，还要使用XFF（X-Forwarded-For）。

<!--more-->

X-Forwarded-For是HTTP扩展头部，不是HTTP/1.1协议中的定义，但是现在基本是标准了，X-Forwarded-For存储了客户端IP和请求链路上各个代理IP。

加入一个请求从IP1位置开始，经过IP2，IP3，IP4三个代理然后到达服务端，那么使用ServletRequest的getRemoteHost获取到的IP是：IP4，X-Forwarded-For中存储的是：

```
X-Forwarded-For: IP1, IP2, IP3
```

我们可以使用X-Forwarded-For中的值来获取真是IP：

```java
// ...
String xff = request.getHeader(X_FORWARDED_FOR);
if (StringUtils.isNotEmpty(xff)) {
  clientIp = xff.split(",")[0];
}
// ...
```

但是如果伪造请求链路，客户端请求的时候手动添加X-Forwarded-For的值，就可能不能获取到正确的IP。



源码：[https://github.com/dachengxi/APIGateway](https://github.com/dachengxi/APIGateway)