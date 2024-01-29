---
title: Gradle排除依赖导致依赖不可用问题
date: 2024-01-29 19:32:28
tags: 
    - Java
categories: 编程经验
---

<meta name="referrer" content="no-referrer"/>

### 一、背景
在新项目中, 引入之前旧项目的公用依赖包之后，发现这个依赖包携带了大量的其他公用依赖树。如：通用返回结构类、通用异常类等等；但是在新项目中，这些都是需要重新定义的。

### 二、问题
由于定义的类名相同，导致引入依赖包时，经常有开发同学引入错误，导致了一些bug。
于是在gradle依赖中，使用`**exclude**`关键字排除这些依赖树。如：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1706524011879-175ec62e-7cd5-42b2-9a62-d32a0aef6e88.png#averageHue=%232d2d2c&clientId=ud281526c-a252-4&from=paste&height=105&id=u0350b5a0&originHeight=116&originWidth=674&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=11970&status=done&style=none&taskId=u75a20701-cc18-44fe-a185-66f70478962&title=&width=612.727259446767)
但是排除之后发现项目无法启动！
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1706524342452-5cddd4f2-4515-4bf4-8ef9-4b925005888c.png#averageHue=%2334322f&clientId=ud281526c-a252-4&from=paste&height=284&id=u95d583fc&originHeight=312&originWidth=1260&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=109747&status=done&style=none&taskId=u691667fc-135a-48fe-aa72-d8fbe4c7113&title=&width=1145.4545206274875)
显然，我们将**field-encrypt**中的**cn.sharing.service:sharing-exception**也排除掉了

原因：使用exclue语句排除一个依赖时，它会影响整个传递依赖关系树。
这可能会导致以下问题：

1. **版本冲突：** 如果**cn.sharing.cloud:field-encrypt:1.2.4**确实需要**cn.sharing.service:sharing-exception**，但与排除的版本冲突，可能会导致运行时错误或不一致性。
2. **功能缺失：** 如果**cn.sharing.cloud:field-encrypt:1.2.4**的某些功能依赖于**cn.sharing.service:sharing-exception:1.0.6**，排除它可能导致缺失这些功能。
### 三、解决
使用**configurations**块来针对特定配置进行排除。
代码：
```bash
configurations {
    excludeSharingException {
        exclude group: 'cn.sharing.service', module: 'sharing-exception'
    }
}
```

