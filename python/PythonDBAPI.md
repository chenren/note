# Python BD-API
python�������ݿ��ͳһ�ӿڹ淶���Բ�ͬ�����ݿ��װ��һ��ͳһ�Ľӿڣ��ײ����ݿ�仯ʱ���ϲ�������ݿ���߼�����û��Ӱ�졣  
�ο���[DB-API��ʹ������](https://blog.csdn.net/weixin_37972723/article/details/80425446)
### DB-API��װ��ʹ��
��mysqlΪ������Ҫ�����ݿ�������ض��ڵ�python�⣺https://dev.mysql.com/downloads/connector/python/  
��ʹ�ã�  
```
import mysql.connector

if __name__ == '__main__':
    dbconfig = {'host': 'localhost',
                'user': 'chenren',
                'password': '1234',
                'database': 'MYSQL80'}

    conn = mysql.connector.connect(**dbconfig)  # �������ݿ�
    cursor = conn.cursor()  # ��ȡ�α�

    sql = 'select * from log'
    cursor.execute(sql)     # ִ�����ݿ����

    cursor.fetchone()       # ��ȡִ�н����һ��
    cursor.fetchmany(3)     # ��ȡִ�н��������
    data = cursor.fetchall()    # ��ȡ����ȫ��ִ�н��
    for row in data:
        print(row)

    conn.commit()     #�������Ҫ���ύ����

    cursor.close()
    conn.close()
```
### MySql ���� 
���� | ˵�� 
:-: | :-:  
systemctl status mysql.service | �鿴sql����״̬  
sudo mysql -uroot -p | ��¼  
show databases; | �鿴���ݿ�  
create/drop database dbname; | ����/ɾ�����ݿ�dbname  
use dbname��| �������ݿ�dbname  
create table tabname(id int not null,name varchar(45),PRIMARY KEY(id)); | ������  
drop table tabname; | ɾ����  
desc tabname; | ��ʾ��  
aler table tabname add column name varchar(4); | �ڱ��в�����  
  
