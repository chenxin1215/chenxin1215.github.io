---
title: ParallelStream的使用与问题
date: 2023-02-08 07:06:01
tags: 
    - Java
categories: 编程经验
---

parallelStream是Java流式集合操作的一种，它会使用多线程执行循环，效率更高。

但是需要注意的是：

1. 数据没有很大的时候不要使用parallelStream可能比Stream还慢一些。

2. parallelStream的返回结果会打乱之前的List的顺序。

3. **parallelStream是线程不安全的**，当你在遍历中使用，例如请求里面的数据时，就会报一个异常，这个异常就是多个线程执行，但是其他线程没有这个请求的数据，所以获取不到，**解决办法是把需要的数据在遍历外面取到，再传递进去就可以解决**