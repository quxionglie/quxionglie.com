---
layout: post
title: "基于左右值的无限级分类算法-oracle实现03"
date: 2012-03-25 10:19
comments: true
categories: oralce
---

<!-- more -->

本文参考了：
Mike Hillyer的《Managing Hierarchical Data in MySQL》
及Yimin的翻译版《MYSQL中分层数据的管理》

#6.检索节点的直接子节点

可以想象一下，你在零售网站上呈现电子产品的分类。当用户点击分类后，你将要呈现该分类下的产品，同时也需列出该分类下的直接子分类，而不是该分类下的全部分类。为此，我们只呈现该节点及其直接子节点，不再呈现更深层次的节点。例如，当呈现PORTABLE ELECTRONICS分类时，我们同时只呈现MP3 PLAYERS、CD PLAYERS和2 WAY RADIOS分类，而不呈现FLASH分类。
要实现它非常的简单，在先前的查询语句上添加HAVING子句：

``` sql
SELECT node.name, (COUNT(parent.name) -(
     sub_tree.depth + 1))   depth
     FROM nested_category   node,
     nested_category   parent,
     nested_category   sub_parent,
(
     SELECT node.name, (COUNT(parent.name) -1)
     AS depth
     FROM nested_category   node,
     nested_category   parent
     WHERE node.lft BETWEEN parent.lft AND parent.rgt
     AND node.name = 'PORTABLE ELECTRONICS'
     GROUP BY node.name
     ORDER BY node.lft
)  sub_tree
WHERE node.lft BETWEEN parent.lft AND parent.rgt
AND node.lft BETWEEN sub_parent.lft AND sub_parent.rgt
AND sub_parent.name = sub_tree.name
GROUP BY node.name,sub_tree.depth
HAVING (COUNT(parent.name) -(sub_tree.depth + 1)) <= 1
```
```
NAME DEPTH
-------------------- ----------
PORTABLE ELECTRONICS 　　0
CD PLAYERS 　　　　　　　　  1 
2 WAY RADIOS 　　　　　      1
MP3 PLAYERS 　　　　　　　　1
```

#7.嵌套集合模型中集合函数的应用

让我们添加一个产品表，我们可以使用它来示例集合函数的应用：

``` sql
CREATE TABLE product(product_id,
  product_id INT   PRIMARY KEY,
  name VARCHAR(40),
  category_id INT NOT NULL
);

-- Create sequence
create sequence SEQ_PRODUCT
minvalue 1
maxvalue 999999999
start with 1
increment by 1
nocache;


INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, '20" TV',3);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, '36" TV',3);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'SuperLCD42"',4);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'UltraPlasma62"',5);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'Value Plasma 38"',5);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'PowerMP35gb',7);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'SuperPlayer1gb',8);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'Porta CD',9);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'CD To go!',9);
INSERT INTO product(product_id, name, category_id) VALUES(SEQ_PRODUCT.NEXTVAL, 'Family Talk 360',10);
```

现在，让我们写一个查询语句，在检索分类树的同时，计算出各分类下的产品数量：

``` sql
SELECT parent.name, COUNT(product.name)
FROM nested_category   node ,
nested_category   parent,
product
WHERE   node.lft BETWEEN parent.lft AND parent.rgt
     AND node.category_id = product.category_id
     GROUP BY parent.name;
```

这条查询语句在检索整树的查询语句上增加了COUNT和GROUP BY子句，同时在WHERE子句中引用了product表和一个自连接。
