---
title: MySQL中查询json类型字段中的中文报错问题
date: 2024-01-27 00:55:03
tags:
    - MySQL
    - 工具方法
categories: 数据库
---
<meta name="referrer" content="no-referrer"/>

### 数据示例：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1705917404024-52c5f7c4-770b-4570-91e5-8d32ede0f627.png#averageHue=%23302f2f&clientId=u579e16a9-38bf-4&from=paste&height=174&id=uc780fb0c&originHeight=191&originWidth=624&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=28346&status=done&style=none&taskId=u6f0b04a0-de45-4631-aec3-29f7da67627&title=&width=567.2727149774223)

### 现象
使用 ->> 进行查询，会提示异常：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1705917455452-262300df-5972-4893-a75a-6d3e462e4f19.png#averageHue=%238e7a54&clientId=u579e16a9-38bf-4&from=paste&height=341&id=uca84dda8&originHeight=375&originWidth=947&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=29857&status=done&style=none&taskId=u2c544d68-6bd5-4f05-8f36-509df4f44b4&title=&width=860.9090722493893)

但是查询英文类型的字段就不会：
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1705917479403-f25767b4-6120-417a-b5cc-6279a931f6c2.png#averageHue=%23777c54&clientId=u579e16a9-38bf-4&from=paste&height=452&id=ua01dbd23&originHeight=497&originWidth=1046&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=58390&status=done&style=none&taskId=u77738be9-f09d-404c-bd34-a4adc3ab97b&title=&width=950.9090702986919)
### 原因
在MySQL中，使用 -> 或 ->> 操作符来访问JSON列中的数据时，需要遵守JSON路径表达式的语法。
JSON路径表达式通常使用英文字符来表示路径的各个部分。
当你使用中文作为JSON键名时，你需要确保路径表达式是合法的。
在例子中，param ->> '$.初筛' 可能会出现问题，因为JSON路径表达式可能不支持直接使用中文字符。
### 解决
为了解决这个问题，可以尝试以下方法：
确保你的数据库和连接字符集支持中文。通常，你需要使用 utf8mb4 字符集来支持包括emoji在内的所有Unicode字符。
将中文键名用双引号包围。在JSON路径表达式中，如果键名包含特殊字符或空格，通常需要用双引号包围。
例如：**param ->> '$."初筛"'**
![image.png](https://cdn.nlark.com/yuque/0/2024/png/29411486/1705917604100-69a71ada-5212-4de3-a569-01053384df35.png#averageHue=%2371865b&clientId=u579e16a9-38bf-4&from=paste&height=428&id=uc93354a3&originHeight=471&originWidth=952&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=50470&status=done&style=none&taskId=u280524a6-883d-44cb-8480-eee393b0d7f&title=&width=865.4545266963238)
