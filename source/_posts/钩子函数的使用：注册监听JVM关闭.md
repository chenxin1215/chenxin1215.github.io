---
title: 钩子函数的使用：注册监听JVM关闭
date: 2023-02-08 07:00:19
tags: 
    - Java
categories: 编程经验
---

代码：
```java

Runtime.getRuntime().addShutdownHook(new Thread(() -> {
    System.out.println("### 收尾的工作");
    int n = 0;
    while (n < 10) {
        System.out.println(Thread.currentThread() + "," + n++);
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}));

```
