# Python BD-API
python�������ݿ��ͳһ�ӿڹ淶���Բ�ͬ�����ݿ��װ��һ��ͳһ�Ľӿڣ��ײ����ݿ�仯ʱ���ϲ�������ݿ���߼�����û��Ӱ�졣  
�ο���[DB-API��ʹ������](https://blog.csdn.net/weixin_37972723/article/details/80425446)
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

    cursor.close()
    conn.close()
```