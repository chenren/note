# FlaskWeb����
## �����¼
1. ����flask web������  
	����ʹ������/�������õķ�ʽ������������
	```
	# ʹ������������������ʹ��export����뻷��������������ѡ��
	export FLASK_APP=main.py
	export FLASK_DEBUG=1
	flask run

	# �ڽű���ʹ��app.run()�����������ɴ�������ѡ��
	app.run(debug=True)
	```
2. ���ݿ�
    ʹ��Flask-SQLAlchemy�������ݿ⣬֧�ֶ������ݿ⡣
    ʹ��MySQLʱURL���ø�ʽ�ж��֣��ֱ��Ӧ��ͬ��python���ݿ������� 
    ```
    # mysqldb, ��֧��python3
    mysql://{user}:{password}@{host}/{database}
    # mysqlconnector
    mysql+mysqlconnector://{user}:{password}@{host}/{database}
    # pymysql
    mysql+pymysql://{user}:{password}@{host}/{database}
    ```
3. Flask-Migrate  
    flask db init  
    flask db migrate # �������ݱ仯�����ɸ��½ű�  
    flask db upgrade # �������ݿ�
    
    ִ�� flask db migrate ʱ���֣� Error: Target database is not up to date.  
    ����migrateʹ��alembic�������ݿ⣬�������ݿ��д���alembic_version���ݱ�����version_num�ֶ�Ӧ�ô����µİ汾�ţ�����migrate�����������������
    ���ļ���version�µ����°汾��д�����ݣ��ٴ�ִ�У���������  
    