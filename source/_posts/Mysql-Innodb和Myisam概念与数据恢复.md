---
title: Mysql--Innodb和Myisam概念与数据恢复
date: 2020-03-06 23:26:01
tags: 
	- 数据库
	- mysql
---

Innodb和Myisam是Mysql常见的两种数据存储引擎。没有研究过Oracle、SQL Server等数据库，所以下面只针对Mysql。

**一、两种方式的数据存储结构：**

　　在Myisam下，数据库的每个数据表都有*.frm、*.YMI和*.YMD三个文件，其中*.frm存储数据表的表结构，*.MYI存储数据表的索引，*.MYD存数数据表的记录数据；

　　在Innodb下，每个数据库下的每个数据表只有一个*.frm存储数据表的表结构，而所有数据库的所有表数据索引、数据记录都全部存储在ibdata1文件中，而ib_logfile0和ib_logfile1是日志文件。

 

**二、数据导入和恢复：**

Case1 Myisam =》 Myisam：

　　由于Myisam的数据表结构、索引、记录数据等信息分别存储在*.frm、*.MYI和*.MYD文件中，所以只需要将源数据库数据表的相应三个文件复制到对应目的数据库文件夹下就可以了。

　　但是如果只有*.frm（假设test.frm）了，那么可以将test.frm复制到对应的数据库目录（假设tmp数据库）之后，在tmp目录下，新建test.MYI和test.MYD文件。此时通过“show tables;”可以看到有test表，但是查看表的数据，如“desc test;”，此时报错无法查看，因为只有test.frm有数据，而test.MYI和test.MYD是新建的无效文件。然后可以通过Mysql自带修复操作“REPAIRTABLE test USE_FRM”修复数据表，然后就可以查看表的数据，但是为空（因为test.frm中不包含数据）。

Case2 Innodb =》 Innodb：

　　由于Innodb下，表结构存在*.frm文件，但是表的数据存储在ibdata1文件中，所以导入时，除了复制*.frm，还要复制源Mysql的data目录下的ibdata1，替换掉目的数据库的ibdata，如果目的数据库中有已存在的其他数据库，此时需要先备份目的数据库的原先data数据，然后将新导入的表格通过其他方式导出，然后在还原原先的data数据，将新导出的数据导入。

　　如果只有*.frm（假设test.frm），没有有效的ibdata1，那么也只能恢复表的结构。先将test.frm复制到另一个数据库中（假设tmp），然后在tmp下新建一个Innodb型的数据表test（有哪些字段不重要），不要添加任何记录。然后将要恢复的test.frm复制到test目录下替代新建test表产生的test.frm，重启mysql之后，新建的test表的结构就和要恢复的test表结构相同

Case3 Myisam =》 Innodb 和 Innodb =》 Myisam：

　　如果有*.frm、*.MYI和*.MYD三个文件，可以通过Case1的方法导入之后，重新导出sql或者其他文件，也可以导入后修改为Innodb，之后通过Case2方式。同样Innodb =》 Myisam的操作类似。

 

　　对于数据库的数据，除了从.frm文件恢复之外，如果有日志文件，从日志文件恢复也是一个一个选择。但是对于数据库，进行移植或这更换系统时，最好还是通过导入导出工具到处相应的sql语句文件。

 

**三、Innodb和Myisam其他区别：**

　　1、Myisam多个数据库和数据表的记录数据是分文件存储的，而Innodb所有表的记录数据都存储在ibdata1文件中，所以当数据表记录比较少，单个表的数据文件比较少时，此时使用Myisam性能会略好于Innodb。但是如果表的数据记录非常多，此时使用Innodb的性能会远远高于Myisam，通过测试可以发现，随着表的记录增加，Innodb的性能降低很少，而Myisam的性能则迅速降低。

　　2、Innodb支持事务操作，而Myisam不支持事务操作。

　　事务操作：就是一组多条sql指令作为一个整体，可以并发操作，但是关键点在于事务内的多个操作必须同时执行完才会提交结果。所以事务内的SQL要么都不做，要么都做。

　　事务实现一般有下面两种方式：

　　Way1 begin、rollback和commit：

　　begin表示开始事务，rollback表示事务回滚，commit表示事务提交。

　　Way2 通过set来改变Mysql默认的自动提交模式：

　　set autocommit=0 禁止自动提交（使用事务，此时每条SQL都会当作事务，必须显示使用commit体提交结果或者rollback撤销结果）
　　set autocommit=1 开启自动提交（禁用事务）