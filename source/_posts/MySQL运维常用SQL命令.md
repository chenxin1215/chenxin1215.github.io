---
title: MySQL运维常用SQL命令
date: 2023-02-03 13:29:10
tags:
    - MySQL
    - 工具方法
categories: 数据库
---

### 常用查询命令

```sql
-- 查询全表数据容量大小
select
table_schema as '数据库',
table_name as '表名',
table_rows as '记录数',
truncate(data_length/1024/1024, 2) as '数据容量(MB)',
truncate(index_length/1024/1024, 2) as '索引容量(MB)'
from information_schema.tables
order by data_length desc, index_length desc;
```

```sql
-- 查询正在运行的SQL及运行状态
select * from information_schema.INNODB_TRX
```

```sql
-- 查询正在被使用的表
show OPEN TABLES where In_use > 0;
```

```sql
-- 查询被锁住了的表
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
```
