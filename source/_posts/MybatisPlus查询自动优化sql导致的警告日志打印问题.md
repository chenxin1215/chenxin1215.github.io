---
title: MybatisPlus查询自动优化sql导致的警告日志打印问题
date: 2023-09-05 22:35:49
tags: 
    - Java
categories: 编程经验
---

<meta name="referrer" content="no-referrer"/>

#### 一、现象

在使用QueryWrapper中的实体构造方法来构造QueryWrapper后，再调用MybatisPlus内置的分页查询接口；
sql可以正常查询，但是会报出以下警告日志：
MybatisPlus版本：3.5.2
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693895781522-3a4d4c8f-d419-4806-a1eb-fb519b56673d.png#averageHue=%232d2d2c&clientId=ufff8bd6c-047b-4&from=paste&height=681&id=uc2e3444b&originHeight=681&originWidth=1883&originalType=binary&ratio=1&rotation=0&showTitle=false&size=152078&status=done&style=none&taskId=u4efda641-8c8f-414e-be01-52ceed24163&title=&width=1883)

#### 二、原因

找到日志位置：
PaginationInnerInterceptor类中的autoCountSql方法
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693897233378-87c628c1-9164-411e-bf6b-474b0f4dbfca.png#averageHue=%232c2c2b&clientId=ufff8bd6c-047b-4&from=paste&height=801&id=ufa6ecd47&originHeight=801&originWidth=1597&originalType=binary&ratio=1&rotation=0&showTitle=false&size=129868&status=done&style=none&taskId=uc82480b3-1653-4e2f-8083-9fbf815d52b&title=&width=1597)

#### 三、解决

方案一：最简单的方法就是不使用Wrapper的构造方法构造查询条件，自己使用代码构造；如下：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693898412222-c428576e-433a-4373-b9b9-b2eeb5e17f73.png#averageHue=%232f2e2c&clientId=ufff8bd6c-047b-4&from=paste&height=269&id=ueb017203&originHeight=269&originWidth=1017&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56787&status=done&style=none&taskId=u52a2d6db-9950-450b-85aa-94dc3da1d58&title=&width=1017)

方案二：如果实在需要使用构造方法构造条件，可以配置不自动优化sql，跳过报错代码；
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693898534016-36ef5cea-fa64-49c1-b6ac-06dea9e5377a.png#averageHue=%23302d2d&clientId=ufff8bd6c-047b-4&from=paste&height=761&id=ue35aa93d&originHeight=761&originWidth=1379&originalType=binary&ratio=1&rotation=0&showTitle=false&size=126030&status=done&style=none&taskId=u608f4453-d1ac-481f-a58b-b946bd5755e&title=&width=1379)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693898550289-c8ae56f2-314e-45af-b0f7-8dc6b5d65a8f.png#averageHue=%232f2e2d&clientId=ufff8bd6c-047b-4&from=paste&height=225&id=uba0b1ad4&originHeight=225&originWidth=902&originalType=binary&ratio=1&rotation=0&showTitle=false&size=42748&status=done&style=none&taskId=u71a30183-62d7-4c6a-a8d3-942dd461cf6&title=&width=902)
