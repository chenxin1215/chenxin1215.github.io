---
title: 关于BigDecimal处理浮点类型出现的问题
date: 2023-02-08 07:10:27
tags: 
    - Java
categories: 编程经验
---

问题：new BigDecimal(3.9) 出现精度问题 

```java
BigDecimal tempModelData = new BigDecimal(1).multiply(new BigDecimal(3.9));
System.out.println("tempModelData = " + tempModelData);

// 输出：tempModelData = 3.899999999999999911182158029987476766109466552734375               
```

原因是3.9是浮点型，自带精度，在new BigdDecimal会把精度带过去

处理：将new BigDecimal(3.9) 改为 **new BigDecimal("3.9")**  字符类型不会带精度