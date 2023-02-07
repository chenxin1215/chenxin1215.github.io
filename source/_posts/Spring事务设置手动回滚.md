---
title: Spring事务设置手动回滚
date: 2023-02-08 06:58:18
tags: Java
---

直接上代码：
```java
    // 设置起始点
    Object savepoint = TransactionAspectSupport.currentTransactionStatus().createSavepoint();
    try {
        this.check(costAdjustId, userId);
    } catch (Exception e) {
        LOGGER.error("批量生成成本调整单 审核调整单异常！");
        errorData.put("第" + i + "行", "审核调整单失败！e：" + e.getLocalizedMessage());

        // 设置手动回滚
        TransactionAspectSupport.currentTransactionStatus().rollbackToSavepoint(savepoint);
    }
```
