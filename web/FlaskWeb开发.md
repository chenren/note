# FlaskWeb开发
## 问题记录
1. 启动flask web服务器  
	可以使用命令/方法调用的方式启动服务器。
	```
	# 使用命令启动，可以以使用export命令导入环境变量配置启动选项
	export FLASK_APP=main.py
	export FLASK_DEBUG=1
	flask run

	# 在脚本中使用app.run()方法启动，可传入启动选项
	app.run(debug=True)
	```
2. 数据库
    使用Flask-SQLAlchemy管理数据库，支持多种数据库。
    使用MySQL时URL配置格式有多种，分别对应不同的python数据库驱动： 
    ```
    # mysqldb, 不支持python3
    mysql://{user}:{password}@{host}/{database}
    # mysqlconnector
    mysql+mysqlconnector://{user}:{password}@{host}/{database}
    # pymysql
    mysql+pymysql://{user}:{password}@{host}/{database}
    ```
3. Flask-Migrate  
    flask db init  
    flask db migrate # 根据数据变化，生成更新脚本  
    flask db upgrade # 更新数据库
    
    执行 flask db migrate 时出现： Error: Target database is not up to date.  
    由于migrate使用alembic管理数据库，会在数据库中创建alembic_version数据表，其中version_num字段应该存最新的版本号，这样migrate命令才能正常工作。
    将文件夹version下的最新版本号写入数据，再次执行，问题解决。  
    