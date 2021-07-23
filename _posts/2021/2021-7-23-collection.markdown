---
layout: post
title:  "java集合类使用场景"
category: java
date:   2021-7-23 13:25:35 +0200

---

## LinkedHashSet 集合适合前端展示有顺序要求的场景，它继承了HashSet，实现Set、Cloneable、java.io.Serializable

## ArrayList 
- 实现List接口，支持扩容，等价于Vector类（除了他不是同步的），
+ `size`,`isEmpty`， `get`， `set`，`iterator`，`listIterator`这几个操作是O(1)时间复杂度，`add`操作是固定摊于时间，增加n个元素需要O(n)时间