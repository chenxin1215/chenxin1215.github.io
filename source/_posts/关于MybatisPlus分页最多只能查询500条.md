---
title: 关于MybatisPlus分页最多只能查询500条
date: 2023-02-08 06:00:44
tags: 
    - Java
categories: 编程经验
---

#### 源码跟踪分析

1. 所有的使用自带的Page对象分页的方法全部被限制了500条，所以很容易想到是对Page类中的size属性做了限制。
2. 查看Page类中的setSize方法的调用方，找到PaginationInterceptor类
   1. ![image](1675757263070-13603bf4-3db0-43c7-843c-bdb0ec7af716.png)
   2. ![image](1675757312653-eb51281d-60fd-4c0e-8669-784e7f5f59ec.png)
   3. ![image](1675757441537-ad827cc7-452c-4088-9cd8-f2737f29e4b8.png)

<a name="rpz1A"></a>

#### 问题解决

修改PaginationInterceptor类中的limit属性。
将以下代码放入application启动类中：

```java
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
         paginationInterceptor.setLimit(-1);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
```
