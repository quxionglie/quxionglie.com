---
layout: post
title: "基于左右值的无限级分类算法-oracle实现04"
date: 2012-03-26 10:29
comments: true
categories: oralce
---
<!--more-->
本文参考了：
Mike Hillyer的《Managing Hierarchical Data in MySQL》
及Yimin的翻译版《MYSQL中分层数据的管理》

#8.新增节点
到现在，我们已经知道了如何去查询我们的树，是时候去关注一下如何增加一个新节点来更
新我们的树了。让我们再一次观察一下我们的嵌套集合图：

![Alt text](/images/2012/oracle-04-1.jpg)


当我们想要在TELEVISIONS和PORTABLE ELECTRONICS节点之间新增一个节点，新节点的lft和rgt 的 值为10和11，所有该节点的右边节点的lft和rgt值都将加2，之后我们再添加新节点并赋相应的lft和rgt值。在MySQL 5中可以使用存储过程来完成，我假设当前大部分读者使用的是MySQL 4.1版本，因为这是最新的稳定版本。所以，我使用了锁表（LOCKTABLES）语句来隔离查询：

``` sql
--增加平行节点
procedure add_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
)
is
  myRight nested_category.rgt%type ;        
BEGIN
  SELECT rgt into myRight FROM nested_category WHERE name = cname;
  UPDATE nested_category SET rgt = rgt + 2 WHERE rgt > myRight;
  UPDATE nested_category SET lft = lft + 2 WHERE lft > myRight;
  INSERT INTO nested_category(CATEGORY_ID,name, lft, rgt) VALUES(SEQ_CATEGORY.nextval,add_name, myRight + 1, myRight + 2); 
  commit; 
exception
    when others then
    rollback;  
end;
```
 
``` sql
EXECUTE pkg_category.add_node('TELEVISIONS','GAME CONSOLES');
```

我们可以检验一下新节点插入的正确性：

``` sql
SELECT  lpad( '+',  (COUNT(parent.name)-1),'+')||node.name name
FROM nested_category node, nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name,node.lft
ORDER BY node.lft;
```

输出结果

```
NAME
----------------------------------
ELECTRONICS
+TELEVISIONS
++TUBE
++LCD
++PLASMA
+GAME CONSOLES
+PORTABLE ELECTRONICS
++MP3 PLAYERS
+++FLASH
++CD PLAYERS
++2 WAY RADIOS
```

如果我们想要在叶子节点下增加节点，我们得稍微修改一下查询语句。让我们在2 WAY RADIOS叶子节点下添加FRS节点吧：

```sql
--在叶子节点下增加节点
procedure add_child_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
)
is
  myLeft nested_category.lft%type ;       
BEGIN
  SELECT lft into myLeft FROM nested_category WHERE name = cname;
  UPDATE nested_category SET rgt = rgt + 2 WHERE rgt > myLeft;
  UPDATE nested_category SET lft = lft + 2 WHERE lft > myLeft;
  INSERT INTO nested_category(CATEGORY_ID, name, lft, rgt) VALUES(SEQ_CATEGORY.nextval, add_name, myLeft + 1, myLeft + 2);
  commit;
exception
    when others then
    rollback; 
end;


EXECUTE pkg_category.add_child_node('2 WAY RADIOS','FRS');


SELECT  lpad( '+',  (COUNT(parent.name)-1),'+')||node.name name
FROM nested_category node, nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name,node.lft
ORDER BY node.lft;
```

输出结果

```
NAME
----------------------------------
ELECTRONICS
+TELEVISIONS
++TUBE
++LCD
++PLASMA
+GAME CONSOLES
+PORTABLE ELECTRONICS
++MP3 PLAYERS
+++FLASH
++CD PLAYERS
++2 WAY RADIOS
+++FRS
```

在这个例子中，我们扩大了新产生的父节点(2 WAY RADIOS节点）的右值及其所有它的右边节点的左右值，之后置新增节点于新父节点之下。正如你所看到的，我们新增的节点已经完全融入了嵌套集合中：

#9.删除节点

最后还有个基础任务，删除节点。删除节点的处理过程跟节点在分层数据中所处的位置有关，删除一个叶子节点比删除一个子节点要简单得多，因为删除子节点的时候，我们需要去处理孤立节点。 
  
删除一个叶子节点的过程正好是新增一个叶子节点的逆过程，我们在删除节点的同时该节点右边所有节点的左右值和该父节点的右值都会减去该节点的宽度值2：

```sql
--删除叶子节点
procedure delete_leaf_node(leaf_name nested_category.name%type)
is
  myLeft nested_category.lft%type ;  
  myRight nested_category.rgt%type ;  
  myWidth integer ;  
BEGIN
    SELECT  lft , rgt , (rgt - lft+ 1) into   myLeft , myRight, myWidth  FROM nested_category
    WHERE name = leaf_name;

    DELETE FROM nested_category WHERE lft BETWEEN  myLeft AND  myRight;
    UPDATE nested_category SET rgt = rgt - myWidth WHERE rgt >  myRight;
    UPDATE nested_category SET lft = lft - myWidth WHERE lft >  myRight;
    commit;
exception
    when others then
    rollback;   
end;

--我们再一次检验一下节点已经成功删除,而且没有打乱数据的层次：
EXECUTE pkg_category.delete_leaf_node('GAME CONSOLES');

SELECT  lpad( '+',  (COUNT(parent.name)-1),'+')||node.name name
FROM nested_category node, nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name,node.lft
ORDER BY node.lft;
```

输出结果

```
NAME
--------------------------------------------------------------------------------
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
+++FRS
```
这个方法可以完美地删除节点及其子节点：

	EXECUTE pkg_category.delete_leaf_node('MP3 PLAYERS');
	
再次验证我们已经成功的删除了一棵子树：

```
NAME
---------------------------- 
ELECTRONICS
+TELEVISIONS
++TUBE
++LCD
++PLASMA
+PORTABLE ELECTRONICS
++CD PLAYERS
++2 WAY RADIOS
+++FRS
```

有时，我们只删除该节点，而不删除该节点的子节点。在一些情况下，你希望改变其名字为占位符，直到替代名字的出现，比如你开除了一个主管（需要更换主管）。在另外一些情况下，你希望子节点挂到该删除节点的父节点下：

```sql
--删除该节点，而不删除该节点的子节点
procedure delete_node_only(node_name nested_category.name%type)
is
  myLeft nested_category.lft%type ;  
  myRight nested_category.rgt%type ;  
  myWidth integer ;  
BEGIN
    SELECT  lft , rgt , (rgt - lft+ 1) into   myLeft , myRight, myWidth  FROM nested_category
    WHERE name = node_name;

    DELETE FROM nested_category WHERE lft = myLeft;
    UPDATE nested_category SET rgt = rgt - 1, lft = lft - 1 WHERE lft BETWEEN  myLeft AND  myRight;
    UPDATE nested_category SET rgt = rgt - 2 WHERE rgt >  myRight;
    UPDATE nested_category SET lft = lft - 2 WHERE lft >  myRight;
    commit;
exception
    when others then
    rollback;
end;
```


在这个例子中，我们对该节点所有右边节点的左右值都减去了2（因为不考虑其子节点，该节点的宽度为2），对该节点的子节点的左右值都减去了1（弥补由于失去父节点的左值造成的裂缝）。我们再一次确认，那些节点是否都晋升了：

```sql
EXECUTE pkg_category.delete_node_only('PORTABLE ELECTRONICS');

SELECT  lpad( '+',  (COUNT(parent.name)-1),'+')||node.name name
FROM nested_category node, nested_category  parent
WHERE node.lft BETWEEN parent.lft AND parent.rgt
GROUP BY node.name,node.lft
ORDER BY node.lft;
```

输出结果  

```
NAME
--------------------- 
ELECTRONICS
+TELEVISIONS
++TUBE
++LCD
++PLASMA
+CD PLAYERS
+2 WAY RADIOS
++FRS

8 rows selected
```

有时，当删除节点的时候，把该节点的一个子节点挂载到该节点的父节点下，而其他节点挂到该节点父节点的兄弟节点下，考虑到篇幅这种情况不在这里解说了。

#10.相关包sql

```sql
create or replace package pkg_category  is
--增加平行节点 
procedure add_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
);

--在叶子节点下增加节点
procedure add_child_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
);

--删除叶子节点
procedure delete_leaf_node(leaf_name nested_category.name%type);

--删除该节点，而不删除该节点的子节点
procedure delete_node_only(node_name nested_category.name%type);

end pkg_category;
```


```sql
create or replace package body pkg_category is

--增加平行节点
procedure add_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
)
is
  myRight nested_category.rgt%type ;        
BEGIN
  SELECT rgt into myRight FROM nested_category WHERE name = cname;
  UPDATE nested_category SET rgt = rgt + 2 WHERE rgt > myRight;
  UPDATE nested_category SET lft = lft + 2 WHERE lft > myRight;
  INSERT INTO nested_category(CATEGORY_ID,name, lft, rgt) VALUES(SEQ_CATEGORY.nextval,add_name, myRight + 1, myRight + 2); 
  commit; 
exception
    when others then
    rollback;  
end;

--在叶子节点下增加节点
procedure add_child_node(
  cname nested_category.name%type,
  add_name nested_category.name%type
)
is
  myLeft nested_category.lft%type ;        
BEGIN
  SELECT lft into myLeft FROM nested_category WHERE name = cname;
  UPDATE nested_category SET rgt = rgt + 2 WHERE rgt > myLeft;
  UPDATE nested_category SET lft = lft + 2 WHERE lft > myLeft;
  INSERT INTO nested_category(CATEGORY_ID, name, lft, rgt) VALUES(SEQ_CATEGORY.nextval, add_name, myLeft + 1, myLeft + 2);
  commit; 
exception
    when others then
    rollback;  
end;
 

--删除叶子节点
procedure delete_leaf_node(leaf_name nested_category.name%type) 
is
  myLeft nested_category.lft%type ;   
  myRight nested_category.rgt%type ;   
  myWidth integer ;   
BEGIN
    SELECT  lft , rgt , (rgt - lft+ 1) into   myLeft , myRight, myWidth  FROM nested_category 
    WHERE name = leaf_name;

    DELETE FROM nested_category WHERE lft BETWEEN  myLeft AND  myRight;
    UPDATE nested_category SET rgt = rgt - myWidth WHERE rgt >  myRight;
    UPDATE nested_category SET lft = lft - myWidth WHERE lft >  myRight;
    commit; 
exception
    when others then
    rollback;    
end;

--删除该节点，而不删除该节点的子节点
procedure delete_node_only(node_name nested_category.name%type)
is
  myLeft nested_category.lft%type ;   
  myRight nested_category.rgt%type ;   
  myWidth integer ;   
BEGIN
    SELECT  lft , rgt , (rgt - lft+ 1) into   myLeft , myRight, myWidth  FROM nested_category 
    WHERE name = node_name;

    DELETE FROM nested_category WHERE lft = myLeft;
    UPDATE nested_category SET rgt = rgt - 1, lft = lft - 1 WHERE lft BETWEEN  myLeft AND  myRight;
    UPDATE nested_category SET rgt = rgt - 2 WHERE rgt >  myRight;
    UPDATE nested_category SET lft = lft - 2 WHERE lft >  myRight;
    commit; 
exception
    when others then
    rollback;
end;

end pkg_category;
```


