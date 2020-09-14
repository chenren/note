## Docker 常用命令
cmd | meaning
:-: | :-:
docker search name | 搜索镜像是否存在
docker pull name:tag | 从服务器拉取镜像
docker images | 查看现有镜像
docker rmi id | 删除镜像
docker run -it name:tag | 从镜像运行容器
docker ps -a | 查看所有容器
docker rm id | 移除容器
docker container prune | 移除所有退出的容器
docker start name/id | 启动容器，stop/restart为停止/重启容器
docker attach name/id | 进入后台运行的容器中
docker login | 登录DockerHub
docker push username/name:tag | 上传镜像到仓库


## Docker 基本使用

1. 使用国内源：  
    通过修改配置文件 /etc/docker/daemon.json  
```
{
  "registry-mirrors": ["https://yourcode.mirror.aliyuncs.com"]
}
```

2. 创建自定义的镜像  
```    
docker run name:tag #从镜像运行容器
... # 进行环境配置
exit()  # 退出docker
docker commit -m "message" 72f1a8a0e394 name:tag
# 其中id可以使用 docker ps -a 查看
```

3. 使用Dockerfile创建镜像  
编写完Dockerfile后执行`docker build -t name:tag .`创建镜像  
一个简单的Dockerfile如下：  
```
FROM python:latest  # 指定基础镜像

ENV FLASK_APP=flasky.py #设定环境变量
ENV FLASK_CONFIG docker

RUN adduser --disabled-password flasky  #相当于在容器中执行命令
USER flasky     # 设定用户

WORKDIR /home/flasky    # 设定工作路径

COPY requirements.txt requirements.txt  # 从本地copy文件到容器
RUN python -m venv venv
RUN venv/bin/pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/

COPY app app
COPY migrations migrations
COPY flasky.py config.py boot.sh ./

EXPOSE 5000     # 设定端口号
ENTRYPOINT ["./boot.sh"]    # 设定入口，容器启动后会自动执行
```

4. 运行容器
    使用`docker run name:tag`运行容器，常用选项： 

cmd | meaning
:-: | :-:
--name xxx | 指定容器名称  
-d | 后台运行  
-e KEY=value | 设置环境变量
-p 8000:5000 | 将机器8000端口映射到容器5000端口，同一个容器映象可以在宿主的不同端口上运行，内部都使用虚拟的5000端口
-i | 打开并保持stdout
-t | 分配一个终端  
--link id:name | 链接一个已经启动的容器,id为链接到的容器id，name为内部使用的别名

5. 运行独立数据库  
```
docker run --name mysql -d -e MYSQL_RANDOM_ROOT_PASSWORD=yes \
-e MYSQL_DATABASE=flasky -e MYSQL_USER=flasky \
-e MYSQL_PASSWORD=1234 \
mysql/mysql-server:5.7
```

## Docker Compose编排容器
1. 编写Compose配置文件docker-compose.yml  
```
version: '3'
services:
  flasky:
    build: .
    ports:
    - "8000:5000"
    env_file:
      - .env
    links:
      - mysql:dbserver
    restart: always
  mysql:
    image: "mysql/mysql-server:5.7"
    env_file:
      - .env-mysql
    restart: always
```
2. 启动应用
    `docker-compose up -d --build` 启动并编译应
3. 停止应用
    `docker-compose down`