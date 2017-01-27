---
layout: post
title: "基于左右值的无限级分类算法-oracle实现02"
date: 2012-03-24 10:04
comments: true
categories: oralce
---

<!-- more -->

本文参考了：
Mike Hillyer的《Managing Hierarchical Data in MySQL》
及Yimin的翻译版《MYSQL中分层数据的管理》


#3.检索单一路径

在嵌套集合模型中，我们可以不用多个自连接就可以检索出单一路径：

``` sql
SELECT parent.name
FROM nested_category    node,
nested_category   parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
AND node.name = 'FLASH'
ORDER BY parent.lft;
```

输出结果

```
NAME
--------------------
ELECTRONICS
PORTABLE ELECTRONICS
MP3 PLAYERS
FLASH
```

#4.检索节点的深度

我们已经知道怎样去呈现一棵整树，但是为了更好的标识出节点在树中所处层次，我们怎样才能检索出节点在树中的深度呢？我们可以在先前的查询语句上增加COUNT函数和GROUP BY子句来实现：

```sql
SELECT node.name, (COUNT(parent.name) - 1) AS depth
FROM nested_category node,
nested_category   parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name  
```

#5.检索子树的深度
当我们需要子树的深度信息时，我们不能限制自连接中的node或parent，因为这么做会打乱数据集的顺序。因此，我们添加了第三个自连接作为子查询，来得出子树新起点的深度
值：

```sql
SELECT node.name, (COUNT(parent.name) -(sub_tree.depth + 1))   depth
  FROM nested_category   node,
  nested_category   parent,
  nested_category   sub_parent,
(
  SELECT node.name, (COUNT(parent.name) - 1)
  AS depth
  FROM nested_category   node,
  nested_category   parent
  WHERE node.lft BETWEEN parent.lft AND parent.rgt
  AND node.name = 'PORTABLE ELECTRONICS'
  GROUP BY node.name 
)  sub_tree
WHERE node.lft BETWEEN parent.lft AND parent.rgt
AND node.lft BETWEEN sub_parent.lft AND sub_parent.rgt
AND sub_parent.name = sub_tree.name
GROUP BY node.name,node.lft,sub_tree.depth
ORDER BY node.lft;
```


这个查询语句可以检索出任一节点子树的深度值，包括根节点。这里的深度值跟你指定的节点有关。
