---
title: mysql 基础语法（二）
date: 2020-04-14 20:21:02
tags: [mysql]
---



### 一、视图

***认识视图***

视图是一个虚拟表，其内容由查询定义。同真实表一样，视图包含一系列带有名称的列和行数据，但视图并不是数据库真实存储的数据表。

视图是从一个、多个表或者视图中导出的表，包含一系列带有名称的数据列和若干条数据行。

视图并不同于数据表，它们的区别在于以下几点：
+	视图不是数据库中真实的表，而是一张虚拟表，其结构和数据是建立在对数据中真实表的查询基础上的。
+	存储在数据库中的查询操作 SQL 语句定义了视图的内容，列数据和行数据来自于视图查询所引用的实际表，引用视图时动态生成这些数据。
+	视图没有实际的物理记录，不是以数据集的形式存储在数据库中的，它所对应的数据实际上是存储在视图所引用的真实表中的。
+	视图是数据的窗口，而表是内容。表是实际数据的存放单位，而视图只是以不同的显示方式展示数据，其数据来源还是实际表。
+	视图是查看数据表的一种方法，可以查询数据表中某些字段构成的数据，只是一些 SQL 语句的集合。从安全的角度来看，视图的数据安全性更高，使用视图的用户不接触数据表，不知道表结构。
+	视图的建立和删除只影响视图本身，不影响对应的基本表。


***创建视图***

创建视图需要注意：
+	创建视图需要足够的访问权限。
+	创建视图的数目没有限制。
+	视图可以嵌套，即从其他视图中检索数据的查询来创建视图。
+	视图不能索引，也不能有关联的触发器、默认值或规则。
+	视图可以和表一起使用。
+	视图不包含数据，所以每次使用视图时，都必须执行查询中所需的任何一个检索操作。如果用多个连接和过滤条件创建了复杂的视图或嵌套了视图，可能会发现系统运行性能下降得十分严重。因此，在部署大量视图应用时，应该进行系统测试。
+	视图定义中`不能引用 TEMPORARY 表（临时表）`，不能创建 TEMPORARY 视图。


需要的权限：
+	CREATE VIEW 权限
+	操作的基础表和其他视图的相关权限

视图定义中引用的表或视图必须存在。但是，创建完视图后，可以删除定义引用的表或视图。可使用 CHECK TABLE 语句检查视图定义是否存在这类问题。

语法如下：
`CREATE VIEW <视图名> AS <SELECT语句>`

实例：
	`create view test2_view as select *from test2;`

***查询视图结构***

语法如下：
	`DESCRIBE 视图名；`

实例：
	`describe test2_view`

查询视图数据：
	`SELECT*FROM test2_view`

***修改视图***

语法如下：
	`ALTER VIEW <视图名> AS <SELECT语句>`

实例：
	`ALTER VIEW test2_view AS SELECT ID FROM test2`
	
> 注：修改视图的名称可以先将视图删除，然后按照相同的定义语句进行视图的创建，并命名为新的视图名称。

***删除视图***

语法如下：
	`DROP VIEW <视图名1> [ , <视图名2> …]`

实例：
	`drop view test2_view`

### 二、存储过程（系列SQL语句）

存储过程是一组为了完成特定功能的 SQL 语句集合。使用存储过程的目的是将常用或复杂的工作预先用 SQL 语句写好并用一个指定名称存储起来，这个过程经编译和优化后存储在数据库服务器中，因此称为存储过程。当以后需要数据库提供与已定义好的存储过程的功能相同的服务时，只需调用“CALL 存储过程名字”即可自动完成。

一个存储过程是一个可编程的函数，它在数据库中创建并保存，一般由 SQL 语句和一些特殊的控制结构组成。当希望在不同的应用程序或平台上执行相同的特定功能时，存储过程尤为合适。

***特点***
+	封装性
+	可增强 SQL 语句的功能和灵活性
+	高性能
+	提高数据库的安全性和数据的完整性


***`DELIMITER`的使用***

在 MySQL 中，服务器处理 SQL 语句默认是以分号作为语句结束标志的。然而，在创建存储过程时，存储过程体可能包含有多条 SQL 语句，这些 SQL 语句如果仍以分号作为语句结束符，那么 MySQL 服务器在处理时会以遇到的第一条 SQL 语句结尾处的分号作为整个程序的结束符，而不再去处理存储过程体中后面的 SQL 语句，这样显然不行。为解决这个问题，通常可使用 DELIMITER 命令将结束命令修改为其他字符。

+	当使用 DELIMITER 命令时，应该避免使用反斜杠“\”字符，因为它是 MySQL 的转义字符。

实例：
```
mysql > DELIMITER ??
-- 将结束语句换为 ??
```


***创建存储过程***

```
CREATE PROCEDURE <过程名> ( [过程参数[,…] ] ) <过程体>
[过程参数[,…] ] 格式
[ IN | OUT | INOUT ] <参数名> <类型>

-- 过程名 存储过程名称
-- 过程参数 
-- 过程体 包含存储过程调用时执行的SQL语句，语句以BEGIN开始，END结束；即 BEGIN-END  
```

【实例】无参数的存储过程：
```
--创建
create procedure showTest2() 
begin
	select*from test2;
end;

--调用
CALL showTest2();

```

【实例】带参数的存储过程：
```
delimiter //
create procedure showTest2_2 (in t_n VARCHAR(3))
BEGIN
	SELECT*from test2 t where t.t_name = t_n;
END//

-- 调用
call showTest2_2('pt')
```

***修改存储过程***

语法如下：
`ALTER PROCEDURE 存储过程名 [ 特征 ... ]`

特征指定了存储过程的特性，可能的取值有：
+	CONTAINS SQL 表示子程序包含 SQL 语句，但不包含读或写数据的语句。
+	NO SQL 表示子程序中不包含 SQL 语句。
+	READS SQL DATA 表示子程序中包含读数据的语句。
+	MODIFIES SQL DATA 表示子程序中包含写数据的语句。
+	SQL SECURITY { DEFINER |INVOKER } 指明谁有权限来执行。
+	DEFINER 表示只有定义者自己才能够执行。
+	INVOKER 表示调用者可以执行。
+	COMMENT 'string' 表示注释信息。

> 注：ALTER PROCEDURE 语句用于修改存储过程的某些特征。如果要修改存储过程的内容，可以先删除原存储过程，再以相同的命名创建新的存储过程；如果要修改存储过程的名称，可以先删除原存储过程，再以不同的命名创建新的存储过程。

***删除存储过程***

语法格式如下：
`DROP { PROCEDURE | FUNCTION } [ IF EXISTS ] <过程名>`

实例：
`drop procedure if exists showTest2;`


### 三、触发器

MySQL 数据库中触发器是一个特殊的存储过程，不同的是执行存储过程要使用 CALL 语句来调用，而触发器的执行不需要使用 CALL 语句来调用，也不需要手工启动，只要一个预定义的事件发生就会被 MySQL自动调用。

引发触发器执行的事件一般如下：
+	增加一条学生记录时，会自动检查年龄是否符合范围要求。
+	每当删除一条学生信息时，自动删除其成绩表上的对应记录。
+	每当删除一条数据时，在数据库存档表中保留一个备份副本。

触发程序的优点如下：
+	触发程序的执行是自动的，当对触发程序相关表的数据做出相应的修改后立即执行。
+	触发程序可以通过数据库中相关的表层叠修改另外的表。
+	触发程序可以实施比 FOREIGN KEY 约束、CHECK 约束更为复杂的检查和操作。

***触发器分类***

MySQL 所支持的触发器有三种：
+	INSERT 触发器
+	UPDATE 触发器
+	DELETE 触发器


1) INSERT 触发器
	在 INSERT 语句执行之前或之后响应的触发器。

使用 INSERT 触发器需要注意以下几点：
+	在 INSERT 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问被插入的行。
+	在 BEFORE INSERT 触发器中，NEW 中的值也可以被更新，即允许更改被插入的值（只要具有对应的操作权限）。
+	对于 AUTO_INCREMENT 列，NEW 在 INSERT 执行之前包含的值是 0，在 INSERT 执行之后将包含新的自动生成值。
2) UPDATE 触发器
	在 UPDATE 语句执行之前或之后响应的触发器。

使用 UPDATE 触发器需要注意以下几点：
+	在 UPDATE 触发器代码内，可引用一个名为 NEW（不区分大小写）的虚拟表来访问更新的值。
+	在 UPDATE 触发器代码内，可引用一个名为 OLD（不区分大小写）的虚拟表来访问 UPDATE 语句执行前的值。
+	在 BEFORE UPDATE 触发器中，NEW 中的值可能也被更新，即允许更改将要用于 UPDATE 语句中的值（只要具有对应的操作权限）。
+	OLD 中的值全部是只读的，不能被更新。

>	注：当触发器设计对触发表自身的更新操作时，只能使用 BEFORE 类型的触发器，AFTER 类型的触发器将不被允许。

3) DELETE 触发器
在 DELETE 语句执行之前或之后响应的触发器。

使用 DELETE 触发器需要注意以下几点：
+	在 DELETE 触发器代码内，可以引用一个名为 OLD（不区分大小写）的虚拟表来访问被删除的行。
+	OLD 中的值全部是只读的，不能被更新。

***错误处理***

+	若对于事务性表，如果触发程序失败，以及由此导致的整个语句失败，那么该语句所执行的所有更改将回滚；对于非事务性表，则不能执行此类回滚，即使语句失败，失败之前所做的任何更改依然有效。
+	若 BEFORE 触发程序失败，则 MySQL 将不执行相应行上的操作。
+	若在 BEFORE 或 AFTER 触发程序的执行过程中出现错误，则将导致调用触发程序的整个语句失败。
+	仅当 BEFORE 触发程序和行操作均已被成功执行，MySQL 才会执行AFTER触发程序。


***创建触发器***

语法格式如下：

```
CREATE TRIGGER <触发器名> 
< BEFORE | AFTER >  --语句执行前|执行后触发
<INSERT | UPDATE | DELETE > 在插入| 更新| 删除 行数据时触发
ON <表名> 
FOR EACH Row
<触发器主体> -- BEGIN…END 复合语句结构
```

实例：
```
-- 在插入数据后执行查询
create TRIGGER  trigger_test2 after insert on test2 for each row
begin 
	insert into test2 value(NEW.ID,NEW.t_name,NEW.test_id);
end;
```

***修改和删除触发器***

删除语法如下：
`DROP TRIGGER [ IF EXISTS ] [数据库名] <触发器名>`


### 四、索引

索引是 MySQL 中一种十分重要的数据库对象。它是数据库性能调优技术的基础，常用于实现数据的快速检索。
在 MySQL 中，通常有以下两种方式访问数据库表的行数据：
+	顺序访问是在表中实行全表扫描。要尽量避免全表扫描。
+	索引访问是通过遍历索引来直接访问表中记录行的方式

***分类***

索引的类型和存储引擎有关，每种存储引擎所支持的索引类型不一定完全相同。根据存储方式的不同，

MySQL 中常用的索引在物理上分为以下两类:

+	B-树索引(BTREE 索引)
	+	B-树索引可以进行全键值、键值范围和键值前缀查询，也可以对查询结果进行 ORDER BY 排序。但 B-树索引必须遵循左边前缀原则，要考虑以下几点约束：
	+	查询必须从索引的最左边的列开始。
	+	查询不能跳过某一索引列，必须按照从左到右的顺序进行匹配。
	+	存储引擎不能使用索引中范围条件右边的列。
+	哈希索引
	+	哈希索引也称为散列索引或 HASH 索引。MySQL 目前仅有 MEMORY 存储引擎和 HEAP 存储引擎支持这类索引。
	+	缺点
		+	MySQL 需要读取表中索引列的值来参与散列计算，散列计算是一个比较耗时的操作。也就是说，相对于 B- 树索引来说，建立哈希索引会耗费更多的时间。
		+	不能使用 HASH 索引排序。
		+	HASH 索引只支持等值比较，如“=”“IN()”或“<=>”。
		+	HASH 索引不支持键的部分匹配，因为在计算 HASH 值的时候是通过整个索引值来计算的。

按逻辑分类：
+	普通索引
+	唯一性索引（UNIQUE 约束 ）
+	主键索引（PRIMARY KEY）
+	空间索引
	+	空间索引主要用于地理空间数据类型 GEOMETRY。
+	全文索引
	+	全文索引只能在 VARCHAR 或 TEXT 类型的列上创建，并且只能在 MyISAM 表中创建。

***索引的使用原则和注意事项***

虽然索引可以加快查询速度，提高 MySQL 的处理性能，但是过多地使用索引也会造成以下弊端：
+	创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加。
+	除了数据表占数据空间之外，每一个索引还要占一定的物理空间。如果要建立聚簇索引，那么需要的空间就会更大。
+	当对表中的数据进行增加、删除和修改的时候，索引也要动态地维护，这样就降低了数据的维护速度。

建立索引应当遵循的原则：
+	在经常需要搜索的列上建立索引，可以加快搜索的速度。
+	在作为主键的列上创建索引，强制该列的唯一性，并组织表中数据的排列结构。
+	在经常使用表连接的列上创建索引，这些列主要是一些外键，可以加快表连接的速度。
+	在经常需要根据范围进行搜索的列上创建索引，因为索引已经排序，所以其指定的范围是连续的。
+	在经常需要排序的列上创建索引，因为索引已经排序，所以查询时可以利用索引的排序，加快排序查询。
+	在经常使用 WHERE 子句的列上创建索引，加快条件的判断速度。

不适合创建索引的环境如下：
+	对于那些在查询中很少使用或参考的列不应该创建索引。因为这些列很少使用到，所以有索引或者无索引并不能提高查询速度。相反，由于增加了索引，反而降低了系统的维护速度，并增大了空间要求。
+	对于那些只有很少数据值的列也不应该创建索引。因为这些列的取值很少，例如人事表的性别列。查询结果集的数据行占了表中数据行的很大比例，增加索引并不能明显加快检索速度。
+	对于那些定义为 TEXT、IMAGE 和 BIT 数据类型的列不应该创建索引。因为这些列的数据量要么相当大，要么取值很少。
+	当修改性能远远大于检索性能时，不应该创建索引。因为修改性能和检索性能是互相矛盾的。当创建索引时，会提高检索性能，降低修改性能。当减少索引时，会提高修改性能，降低检索性能。因此，当修改性能远远大于检索性能时，不应该创建索引。


***创建索引***

语法格式如下：
```
-- 通过建立索引语句
CREATE INDEX  <索引名> ON <表名> (<列名> [<长度>] [ ASC | DESC])

-- 通过建表语句
CONSTRAINT PRIMARY KEY [索引类型] (<列名>,…)  --主键索引	
KEY | INDEX [<索引名>] [<索引类型>] (<列名>,…) -- 普通索引
UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…) -- 唯一索引
FOREIGN KEY <索引名> <列名>

-- 使用 ALTER TABLE 

ADD INDEX [<索引名>] [<索引类型>] (<列名>,…) -- 普通索引
ADD PRIMARY KEY [<索引类型>] (<列名>,…) --主键索引
ADD UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…) -- 唯一索引
ADD FOREIGN KEY [<索引名>] (<列名>,…) -- 普通索引


```

***修改与删除索引***
1) 使用 DROP INDEX 语句

`DROP INDEX <索引名> ON <表名>`

2) 使用 ALTER TABLE 语句
根据 ALTER TABLE 语句的语法可知，该语句也可以用于删除索引。具体使用方法是将 ALTER TABLE 语句的语法中部分指定为以下子句中的某一项。
+	DROP PRIMARY KEY：表示删除表中的主键。一个表只有一个主键，主键也是一个索引。
+	DROP INDEX index_name：表示删除名称为 index_name 的索引。
+	DROP FOREIGN KEY fk_symbol：表示删除外键。


### 五、用户

***用户***
```
-- 创建 用户

	CREATE USER <用户名> [ IDENTIFIED ] BY [ PASSWORD ] <口令>
--  create user TT identified by 'tt'

-- 修改用户名
RENAME USER <旧用户> TO <新用户>

-- alter user TT to tt;

-- 删除用户
DROP USER <用户名1> [ , <用户名2> ]…
```

***用户授权***

```
GRANT
<权限类型> [ ( <列名> ) ] [ , <权限类型> [ ( <列名> ) ] ]
ON <对象> <权限级别> TO <用户>
其中<用户>的格式：
<用户名> [ IDENTIFIED ] BY [ PASSWORD ] <口令>
[ WITH GRANT OPTION]
| MAX_QUERIES_PER_HOUR <次数>
| MAX_UPDATES_PER_HOUR <次数>
| MAX_CONNECTIONS_PER_HOUR <次数>
| MAX_USER_CONNECTIONS <次数>
```

数据库-授权类型：
+	SELECT：表示授予用户可以使用 SELECT 语句访问特定数据库中所有表和视图的权限。
+	INSERT：表示授予用户可以使用 INSERT 语句向特定数据库中所有表添加数据行的权限。
+	DELETE：表示授予用户可以使用 DELETE 语句删除特定数据库中所有表的数据行的权限。
+	UPDATE：表示授予用户可以使用 UPDATE 语句更新特定数据库中所有数据表的值的权限。
+	REFERENCES：表示授予用户可以创建指向特定的数据库中的表外键的权限。
+	CREATE：表示授权用户可以使用 CREATE TABLE 语句在特定数据库中创建新表的权限。
+	ALTER：表示授予用户可以使用 ALTER TABLE 语句修改特定数据库中所有数据表的权限。
+	SHOW VIEW：表示授予用户可以查看特定数据库中已有视图的视图定义的权限。
+	CREATE ROUTINE：表示授予用户可以为特定的数据库创建存储过程和存储函数的权限。
+	ALTER ROUTINE：表示授予用户可以更新和删除数据库中已有的存储过程和存储函数的权限。
+	INDEX：表示授予用户可以在特定数据库中的所有数据表上定义和删除索引的权限。
+	DROP：表示授予用户可以删除特定数据库中所有表和视图的权限。
+	CREATE TEMPORARY TABLES：表示授予用户可以在特定数据库中创建临时表的权限。
+	CREATE VIEW：表示授予用户可以在特定数据库中创建新的视图的权限。
+	EXECUTE ROUTINE：表示授予用户可以调用特定数据库的存储过程和存储函数的权限。
+	LOCK TABLES：表示授予用户可以锁定特定数据库的已有数据表的权限。
+	ALL 或 ALL PRIVILEGES：表示以上所有权限。



表权限-授权类型：
+	SELECT：授予用户可以使用 SELECT 语句进行访问特定表的权限。
+	INSERT：授予用户可以使用 INSERT 语句向一个特定表中添加数据行的权限。
+	DELETE：授予用户可以使用 DELETE 语句从一个特定表中删除数据行的权限。
+	DROP：授予用户可以删除数据表的权限。
+	UPDATE：授予用户可以使用 UPDATE 语句更新特定数据表的权限。
+	ALTER：授予用户可以使用 ALTER TABLE 语句修改数据表的权限。
+	REFERENCES：授予用户可以创建一个外键来参照特定数据表的权限。
+	CREATE：授予用户可以使用特定的名字创建一个数据表的权限。
+	INDEX：授予用户可以在表上定义索引的权限。
+	ALL 或 ALL PRIVILEGES：所有的权限名。

列权限：
<权限类型>的值只能指定为 SELECT、INSERT 和 UPDATE，同时权限的后面需要加上列名列表 column-list。

实例：

```
-- 授权数据库权限
grant all on toolweb to TT;

-- 授权数据库中的表
grant ALL ON csg.ttt to TT;

-- 授权表中的指定列
grant select(USERID) ON csg.t to TT;
```


***删除用户权限***
```
REVOKE <权限类型> [ ( <列名> ) ] [ , <权限类型> [ ( <列名> ) ] ]…
ON <对象类型> <权限名> FROM <用户1> [ , <用户2> ]…
```

```
REVOKE ALL PRIVILEGES, GRANT OPTION
FROM user <用户1> [ , <用户2> ]…
```