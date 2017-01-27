---
layout: post
title: "基于左右值的无限级分类算法-oracle实现01"
date: 2012-03-23 14:58
comments: true
categories: oralce
---

本文参考了：
Mike Hillyer的《Managing Hierarchical Data in MySQL》
及Yimin的翻译版《MYSQL中分层数据的管理》

<!-- more -->

建表sql

``` sql
CREATE TABLE nested_category (
     category_id INT PRIMARY KEY,
     name VARCHAR(20) NOT NULL,
     lft INT NOT NULL,
     rgt INT NOT NULL
);

-- Create sequence
create sequence SEQ_CATEGORY
minvalue 1
maxvalue 999999999999999999999
start with 1
increment by 1
nocache;

INSERT INTO nested_category VALUES(1,'ELECTRONICS',1,20);
INSERT INTO nested_category VALUES(2,'TELEVISIONS',2,9);
INSERT INTO nested_category VALUES(3,'TUBE',3,4);
INSERT INTO nested_category VALUES(4,'LCD',5,6);
INSERT INTO nested_category VALUES(5,'PLASMA',7,8);
INSERT INTO nested_category VALUES(6,'PORTABLE ELECTRONICS',10,19);
INSERT INTO nested_category VALUES(7,'MP3 PLAYERS',11,14);
INSERT INTO nested_category VALUES(8,'FLASH',12,13);
INSERT INTO nested_category VALUES(9,'CD PLAYERS',15,16);
INSERT INTO nested_category VALUES(10,'2 WAY RADIOS',17,18);
```

![Alt text](/images/2012/oracle-01-1.jpg)


##1.检索整树

我们可以通过自连接把父节点连接到子节点上来检索整树，是因为子节点的lft值总是在其

父节点的lft值和rgt值之间：

``` sql
SELECT node.name
FROM nested_category  node,
nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
AND parent.name = 'ELECTRONICS'
ORDER BY node.lft;
```
```
ELECTRONICS
TELEVISIONS
TUBE
LCD
PLASMA
PORTABLE ELECTRONICS
MP3 PLAYERS
FLASH
CD PLAYERS
2 WAY RADIOS
```

缩进显示层次

``` sql
SELECT  lpad( '+',  (COUNT(parent.name)-1),'+')||node.name name
FROM nested_category node, nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name,node.lft
ORDER BY node.lft;
```

```
NAME
-------------------------------------
ELECTRONICS
+TELEVISIONS
++TUBE
++LCD
++PLASMA
+PORTABLE ELECTRONICS
++MP3 PLAYERS
+++FLASH
++CD PLAYERS
++2 WAY RADIOS
```

##2.检索所有叶子节点

检索出所有的叶子节点，使用嵌套集合模型的方法比邻接表模型的LEFT JOIN方法简单多

了。如果你仔细得看了nested_category表，你可能已经注意到叶子节点的左右值是连续的。要检索出叶子节点，我们只要查找满足rgt=lft+1的节点：

```
SELECT name
FROM nested_category
WHERE rgt = lft + 1;
```
```
NAME
--------------------
TUBE
LCD
PLASMA
FLASH
CD PLAYERS
2 WAY RADIOS
```

