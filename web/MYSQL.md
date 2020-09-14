## MySql 命令 
命令 | 说明 
:-: | :-:  
sudo service mysql start/stop/restart | 启动/停止/重启服务
systemctl status mysql.service | 查看sql服务状态  
sudo mysql -uroot -p | 登录  
show databases; | 查看数据库  
create/drop database dbname; | 创建/删除数据库dbname  
use dbname；| 进入数据库dbname  
create table tabname(id int not null,name varchar(45),PRIMARY KEY(id)); | 创建表  
drop table tabname; | 删除表  
desc tabname; | 显示表  
aler table tabname add column name varchar(4); | 在表中插入列  

1.允许本地访问的用户（127.0.0.1）  
create user zhrt@localhost identified by '123456';  
2.允许外网IP访问的用户  
create user 'zhrt'@'%' identified by '123456';  
用户分配权限  
授予用户在本地服务器对该数据库的全部权限  
grant all privileges on dbname.* to zhrt@localhost identified by '123456';  
授予用户通过外网IP对于该数据库的全部权限  
grant all privileges on dbname.* to 'zhrt'@'%' identified by '123456';  
刷新权限  
flush privileges;  
转字符集  
alter table users convert to character set utf8;  

Mysql Workbench登录问题的解决  
下载完Mysql Workbench后，在cmd上可以连接Mysql，但是Mysql workbench连接不上。
其实是因为Mysql 加密方式改变。
解决方法：  
在cmd黑窗口中启动MySql服务器后输入：  
ALTER USER ‘root’@’localhost’ IDENTIFIED WITH mysql_native_password BY ‘your password’;  
FLUSH PRIVILEGES;  

## 表操作
创建表：  
```
CREATE TABLE tab(
c1 int NOT NULL AUTO_INCREMENT, # 非空，且自增
c2 vchar(64) DEFAULT 'aaaa',    # 指定默认值
PRIMARY KEY(c1)     # 指定主键
FOREIGN KEY(P_Id) REFERENCES tab2(P_Id) # 指定外键
KEY(c1)     # 指定创建索引的列
)DEFAULT CHARACTER SET utf8     # 指定默认字符集
COLLATE xxx                     # 指定校对，影响字符的比较和排序方式
ENGINE=InnoDB;
```
命令 | 说明 
:-: | :-:  
DROP TABLE tab | 删除表  
RENAME TABLE tab TO tab2 | 重命名表  
ALTER TABLE tab ADD c CHAR(20) | 表中插入列  
ALTER TABLE tab DROP COLUMN c | 表中删除列  
ALTER TABLE tab1 ADD CONSTRAINT fk FOREIGN KEY(c1) REFERENCES tab2(c1) | 增加外键，定义名称为fk  
ALTER TABLE tab1 DROP FOREIGN KEY fk | 删除外键  
ALTER TABLE tab1 CONVERT TO CHARACTER SET utf8 | 更改字符集  
ALTER TABLE tab1 ADD INDEX indexname(c1, c2) | 对c1,c2创建索引  
ALTER TABLE tab1 ADD KEY (c1(7))  | 对c1的前7个字符创建前缀索引  


## SELECT
命令 | 说明 
:-: | :-:  
SELECT DISTINCT column FROM tabname; | 输出不重复的数据，如果作用于多列，只有多列都相同的才会过滤掉  
SELECT column FROM tabname LIMIT 2 OFFSET 3 | 输出2行，从第3行开始（从0开始计数）。也可以使用 LIMIT 3,2  
SELECT * FROM tabname ORDER BY c1,c2 DESC | 排序，其中DESC指定c2为逆序排序，不写为升序。DESC作用于临近的一项  
SELECT * FROM tabname WHERE c1 BETWEEN 1 AND 3 | 范围查询  
SELECT * FROM tabname WHERE c1 IN (1,2,3) | 范围查询，列出所有取值  
SELECT * FROM tabname WHERE c1 LIKE '%aa_' | 通配符匹配，%代表任意多个字符，\_代表任意一个字符  
SELECT * FROM tabname WHERE c1 REGEXP 'ab*' | 正则表达式匹配  
SELECT Concat(RTrim(c1), '(', LTrim(c2), ')') AS some_name FROM tabname | Concat拼接字段，RTrim删除右空格，AS取别名  
SELECT c1, COUNT(*) FROM tabname GROUP BY c1 | COUNT计算行数，使用GROUP BY分组。这里以c1列分组，count计算各分组的行数  
SELECT c1, COUNT(*) FROM tabname GROUP BY c1 HAVING COUNT(\*) > 2 | 使用HAVING可以过滤分组  
SELECT * FROM tab1 INNER JOIN tab2 ON tab1.c1 = tab2.c2 | 内部连接，只有满足条件的列才会被列出  
SELECT ... UNION SELECT ... | UNION组合多条select语句的结果，每条语句必须有相同的列，数据类型需要兼容，UNION ALL可关闭自动去重  

## INSERT、UPDATE、DELETE
命令 | 说明 
:-: | :-:  
INSERT INTO tabname(c1, c2) VALUES (1, 2),(3, 4) | 插入多行并设定指定列  
INSERT INTO tab1(c1, c2) SELECT c1, c2 FROM tab2 | 将查询结果插入另一个表，需要匹配列的位置  
UPDATE tab SET c1=1, c2 =2 WHERE ... | 更新表中满足条件的行
DELETE FROM tabname WHERE ... | 删除表中满足条件的行
TRUNCATE TABLE tabname  | 删除表中所有行，保留表结构，比DELETE快。



## 全文本搜索 
参考：[MySQL 中文全文检索](https://www.cnblogs.com/xuey/p/11631102.html)  
全文本搜索与LIKE/REGEXP相比：  
1. 查询性能更优，LIKE/REGEXP需要扫描全表，而全文本搜索使用索引查找  
2. 全文本搜索能根据匹配度进行排序。  
MySQL创建指定列中各词的一个索引，搜索可以针对这些词进行。  
这样，MySQL可以快速有效地决定哪些词匹配（哪些行包含它们），
哪些词不匹配，它们匹配的频率，等等。  

使用中文的全文本搜索需要注意，MySQL默认使用ngram进行分词，可以配置n的大小。  
启动时配置： `mysqld --ngram_token_size=2`,或者修改配置文件  

### 创建全文索引的方式  
1. 创建表时创建，使用中文需要加上 `WITH PARSER ngram`
```
CREATE TABLE articles (
    id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
    title VARCHAR (200),
    body TEXT,
    FULLTEXT (title, body) WITH PARSER ngram
) ENGINE = INNODB;
```

2. 通过 alter table 的方式来添加
```
ALTER TABLE articles ADD FULLTEXT INDEX ft_index (title,body) WITH PARSER ngram;
```

3. 创建索引的方式
```
CREATE FULLTEXT INDEX ft_index ON articles (title,body) WITH PARSER ngram;
```

### 全文检索模式
常用的全文检索模式有两种：
1. 自然语言模式(NATURAL LANGUAGE MODE)  
自然语言模式是MySQL 默认的全文检索模式。自然语言模式不能使用操作符，不能指定关键词必须出现或者必须不能出现等复杂查询。
2. BOOLEAN模式(BOOLEAN MODE)  
BOOLEAN模式可以使用操作符，可以支持指定关键词必须出现或者必须不能出现或者关键词的权重高还是低等复杂查询。  

操作符 | 说明 
:-: | :-:   
\+ | 包含，词必须存在  
\- | 排除，词必须不出现  
\> | 包含，而且增加等级值  
< | 包含，且减少等级值  
() | 把词组成子表达式（允许这些子表达式作为一个组被包含、排除、排列等）  
~ | 取消一个词的排序值  
\* | 词尾的通配符  
"" | 定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）  

BOOLEAN模式可以在不创建全文索引时使用，但速度慢。使用BOOLEAN模式的例子，其中+aa代表一定需要出现aa，-bb代表一定不能出现bb：
```
SELECT * FROM articles
WHERE MATCH (title,body)
AGAINST ('+aa -bb' IN BOOLEAN MODE); 
```

## 视图
视图是一个虚拟表，是sql的查询结果，其内容由查询定义。
同真实的表一样，视图包含一系列带有名称的列和行数据，在使用视图时动态生成。
视图的数据变化会影响到基表，基表的数据变化也会影响到视图。  
使用视图的好处：  
1. 重用SQL查询语句，简化后续查询
2. 可以授予用户访问视图的权限，而不是全表的权限，起到保护数据的作用。
问题：  
视图不包含数据，使用视图时数据库重新执行查询操作，自动更新数据，因此复杂视图会降低性能。  

命令 | 说明  
:-: | :-:  
CREATE VIEW viewname AS (SELECT ...) | 从查询语句创建视图  
CREATE OR REPLACE VIEW viewname AS (SELECT ...) | 创建或替换视图  
SHOW CREATE VIEW viewname | 显示视图对应的查询语句  
DROP VIEW viewname | 删除视图  

## 存储过程
存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，
用户可通过指定存储过程的名字并给定参数(需要时)来调用执行。类似于函数，主要的作用有：  
1. 代码的封装与重用
2. 编译后执行性能高

创建存储过程：  
```
CREATE PROCEDURE proc_name
(
IN p1 DECIMAL(8,2),     # 声明输入参数， DECIMAL为精确类型，小数点前8位，后2位
OUT p2 DECIMAL(8,2),    # 声明输出参数
INOUT p3 int)           # 声明输入输出参数
BEGIN
DECLARE v INT DEFAULT 1;            # 声明临时变量
SELECT c1 INTO p2 FROME tabname;    # SELECT INTO 将检索值保存到变量
END;
```

调用存储过程：  
CALL proc_name(@p1, @p2, @p3)   # 调用存储过程，传入参数
SELECT @p2                      # 将传出的值显示出来

删除存储过程：  
DROP PROCEDURE IF EXISTS proc_name

## 游标
MYSQL检索操作的结果是由多行组成的数据集，游标可以在这些数据集上逐行的进行操作。  
```
CREATE PROCEDURE proc_name()
BEGIN
DECLARE o INT;
DECLARE done BOOLEAN DEFAULT 0;
DECLARE cursor1 CURSOR FOR SELECT ...;  # 根据SELECT语句创建游标
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1;   # 声明CONTINUE HANDLER条件，在每次循环时都会执行。
OPEN cursor1;           # 打开游标
REPEAT
FETCH cursor1 INTO o;   # 从游标中取下一个值
UNTIL done END REPEAT;  # 循环判断条件
CLOSE cursor1;          # 关闭游标

END;
```

## 触发器
触发器是在一些事件发生时（某些SQL语句执行时）自动进行一些处理。  
支持触发器的语句：INSERT、UPDATE、DELETE  
```
# 创建触发器，在插入之后执行。FOR EACH ROW 指明对插入的每行都进行操作
CREATE TRIGGER t1 AFTER INSERT ON tabname FOR EACH ROW ...;

DROP TRIGGER t1;    # 删除
```

1. INSERT触发器  
在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行；  
`CREATE TRIGGER t1 AFTER INSERT ON tabname FOR EACH ROW SELECT NOW.c1;`
在BEFORE INSERT触发器中，NEW中的值也可以被更新（允许更改被插入的值）；  
对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值。  
2. DELETE触发器    
在DELETE触发器代码内，你可以引用一个名为OLD的虚拟表，访问被删除的行  
3. UPDATE触发器    
在UPDATE触发器代码中，你可以引用一个名为OLD的虚拟表访问以前（UPDATE语句前）的值，
引用一个名为NEW的虚拟表访问新更新的值；  
在BEFORE UPDATE触发器中，NEW中的值可能也被更新（允许更改将要用于UPDATE语句中的值）  

## 事务
```
START TANSACTION;       # 开始事务  
DELETE ...
SAVEPOINT p1;           # 设置保存点
COMMIT;                 # 提交事务
ROLLBACK TO p1;         # 回退到保存点
```

## 账户管理
命令 | 说明 
:-: | :-:  
CREATE USER 'name'@'%' IDENTIFIED BY 'xxx' | 创建用户，指定密码，%指明允许用户通过外网访问，也可以使用@localhost指明只允许本地访问  
DROP USER name | 删除用户  
GRANT SELECT ON dbname.tabname TO username | 为用户赋予在表tabname上的SELECT权限  
REVOKE SELECT ON dbname.tabname FROM username | 撤销权限，可以用GRANT/REVOKE ALL 为服务器上所有数据库分配权限  
SET PASSWORD FOR username = Password('xxx') | 修改密码，需要用Password函数加密  


权限表：  

权限 | 说明 
:-: | :-:  
ALL | 除GRANT OPTION外的所有权限  
ALTER | 使用ALTER TABLE  
ALTER ROUTINE | 使用ALTER PROCEDURE和DROP PROCEDURE  
CREATE | 使用CREATE TABLE  
CREATE ROUTINE | 使用CREATE PROCEDURE  
CREATE TEMPORARY TABLES | 使用CREATE TEMPORARY TABLE  
CREATE USER | 使用CREATE USER、DROP USER、RENAME USER和REVOKE ALL PRIVILEGES  
CREATE VIEW | 使用CREATE VIEW  
DELETE | 使用DELETE  
DROP | 使用DROP TABLE  
EXECUTE | 使用CALL和存储过程  
FILE | 使用SELECT INTO OUTFILE和LOAD DATA INFILE  
GRANT OPTION | 使用GRANT和REVOKE  
INDEX | 使用CREATE INDEX和DROP INDEX  
INSERT | 使用INSERT  
LOCK TABLES | 使用LOCK TABLES  
PROCESS | 使用SHOW FULL PROCESSLIST  
RELOAD | 使用FLUSH  
REPLICATION CLIENT | 服务器位置的访问  
REPLICATION SLAVE | 由复制从属使用  
SELECT | 使用SELECT  
SHOW DATABASES | 使用SHOW DATABASES  
SHOW VIEW | 使用SHOW CREATE VIEW  
SHUTDOWN | 使用mysqladmin shutdown（用来关闭MySQL）  
SUPER | 使用CHANGE MASTER、KILL、LOGS、PURGE、MASTER和SET GLOBAL。还允许mysqladmin调试登录  
UPDATE | 使用UPDATE  
USAGE | 无访问权限  

