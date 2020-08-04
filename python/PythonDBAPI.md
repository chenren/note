# Python BD-API
python访问数据库的统一接口规范，对不同的数据库封装了一套统一的接口，底层数据库变化时对上层操作数据库的逻辑代码没有影响。  
参考：[DB-API的使用流程](https://blog.csdn.net/weixin_37972723/article/details/80425446)
### DB-API安装与使用
以mysql为例，需要到数据库官网下载对于的python库：https://dev.mysql.com/downloads/connector/python/  
简单使用：  
```
import mysql.connector

if __name__ == '__main__':
    dbconfig = {'host': 'localhost',
                'user': 'chenren',
                'password': '1234',
                'database': 'MYSQL80'}

    conn = mysql.connector.connect(**dbconfig)  # 连接数据库
    cursor = conn.cursor()  # 获取游标

    sql = 'select * from log'
    cursor.execute(sql)     # 执行数据库操作

    cursor.fetchone()       # 获取执行结果，一行
    cursor.fetchmany(3)     # 获取执行结果，多行
    data = cursor.fetchall()    # 获取余下全部执行结果
    for row in data:
        print(row)

    conn.commit()     #如果有需要，提交事务

    cursor.close()
    conn.close()
```
### MySql 命令 
命令 | 说明 
:-: | :-:  
systemctl status mysql.service | 查看sql服务状态  
sudo mysql -uroot -p | 登录  
show databases; | 查看数据库  
create/drop database dbname; | 创建/删除数据库dbname  
use dbname；| 进入数据库dbname  
create table tabname(id int not null,name varchar(45),PRIMARY KEY(id)); | 创建表  
drop table tabname; | 删除表  
desc tabname; | 显示表  
aler table tabname add column name varchar(4); | 在表中插入列  
  
