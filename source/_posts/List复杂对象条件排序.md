---
title: List复杂对象条件排序
date: 2023-02-08 07:09:43
tags: 
    - Java
categories: 编程经验
---

上代码：
```java

    // 根据创建时间排序
    Collections.sort(receivableDataList, new Comparator<SaveAccruedItemData>() {
        @Override
        public int compare(SaveAccruedItemData o1, SaveAccruedItemData o2) {
            // 需要比较的属性
            Date key1 = o1.getOrderCreateTime();
            Date key2 = o2.getOrderCreateTime();
            // 如果key1大返回1 否则（如果key1小于key2 返回-1 否则返回0）
            return key1.compareTo(key2);
        }
    });

```
