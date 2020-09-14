# Python BD-API
python访问数据库的统一接口规范，对不同的数据库封装了一套统一的接口，底层数据库变化时对上层操作数据库的逻辑代码没有影响。  
参考：[DB-API的使用流程](https://blog.csdn.net/weixin_37972723/article/details/80425446)
### DB-API安装与使用
以mysql为例，需要到数据库官网下载对于的python库：https://dev.mysql.com/downloads/connector/python/  
简单使用：  
```
import mysql.connector

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
