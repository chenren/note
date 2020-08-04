# FlaskWeb开发
## 基础知识
1. 启动flask web服务器  
	可以使用命令/方法调用的方式启动服务器。
	```
	# 使用命令启动，可以以使用export命令导入环境变量配置启动选项
	export FLASK_APP=main.py
	export FLASK_DEBUG=1_
	flask run_

	# 在脚本中使用app.run()方法启动，可传入启动选项
	app.run(debug=True)
	```