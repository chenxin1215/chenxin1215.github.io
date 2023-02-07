---
title: Java配合Mybatis实现分词匹配
date: 2023-02-08 07:02:05
tags: Java
---

Java代码：
```java
    public List<Label> getRecommendLabel(String forumName) {
    if (forumName == null || "".equals(forumName)) {
        return null;
    }
    // 将forumName 拆分分词 得到一个List
    Result parse = ToAnalysis.parse(forumName);
    List<Term> terms = parse.getTerms();
    
    // 使用这个list进行遍历模糊匹配
    List<Label> labels = labelMapper.getRecommendLabel(terms);

    return labels;
}
```

MybatisXml:
```xml
    select
    <include refid="Base_Column_List"/>
    from label
    where state = 1 
    and(
    <foreach collection="list" item="term" separator="or">
        label_name LIKE CONCAT('%', #{term.name}, '%')
    </foreach>
)

```