---
title: MySQL查询全表数据容量及记录数
date: 2023-02-03 13:29:10
tags: MySQL
---

## 查询SQL

```mysql
    select
    table_schema as '数据库',
    table_name as '表名',
    table_rows as '记录数',
    truncate(data_length/1024/1024, 2) as '数据容量(MB)',
    truncate(index_length/1024/1024, 2) as '索引容量(MB)'
    from information_schema.tables
    -- where TABLE_SCHEMA = 'skywalking'
    order by data_length desc, index_length desc;
```