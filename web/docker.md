## Docker ��������
cmd | meaning
:-: | :-:
docker search name | ���������Ƿ����
docker pull name:tag | �ӷ�������ȡ����
docker images | �鿴���о���
docker rmi id | ɾ������
docker run -it name:tag | �Ӿ�����������
docker ps -a | �鿴��������
docker rm id | �Ƴ�����
docker container prune | �Ƴ������˳�������
docker start name/id | ����������stop/restartΪֹͣ/��������
docker attach name/id | �����̨���е�������
docker login | ��¼DockerHub
docker push username/name:tag | �ϴ����񵽲ֿ�


## Docker ����ʹ��

1. ʹ�ù���Դ��  
    ͨ���޸������ļ� /etc/docker/daemon.json  
```
{
  "registry-mirrors": ["https://yourcode.mirror.aliyuncs.com"]
}
```

2. �����Զ���ľ���  
```    
docker run name:tag #�Ӿ�����������
... # ���л�������
exit()  # �˳�docker
docker commit -m "message" 72f1a8a0e394 name:tag
# ����id����ʹ�� docker ps -a �鿴
```

3. ʹ��Dockerfile��������  
��д��Dockerfile��ִ��`docker build -t name:tag .`��������  
һ���򵥵�Dockerfile���£�  
```
FROM python:latest  # ָ����������

ENV FLASK_APP=flasky.py #�趨��������
ENV FLASK_CONFIG docker

RUN adduser --disabled-password flasky  #�൱����������ִ������
USER flasky     # �趨�û�

WORKDIR /home/flasky    # �趨����·��

COPY requirements.txt requirements.txt  # �ӱ���copy�ļ�������
RUN python -m venv venv
RUN venv/bin/pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/

COPY app app
COPY migrations migrations
COPY flasky.py config.py boot.sh ./

EXPOSE 5000     # �趨�˿ں�
ENTRYPOINT ["./boot.sh"]    # �趨��ڣ�������������Զ�ִ��
```

4. ��������
    ʹ��`docker run name:tag`��������������ѡ� 

cmd | meaning
:-: | :-:
--name xxx | ָ����������  
-d | ��̨����  
-e KEY=value | ���û�������
-p 8000:5000 | ������8000�˿�ӳ�䵽����5000�˿ڣ�ͬһ������ӳ������������Ĳ�ͬ�˿������У��ڲ���ʹ�������5000�˿�
-i | �򿪲�����stdout
-t | ����һ���ն�  
--link id:name | ����һ���Ѿ�����������,idΪ���ӵ�������id��nameΪ�ڲ�ʹ�õı���

5. ���ж������ݿ�  
```
docker run --name mysql -d -e MYSQL_RANDOM_ROOT_PASSWORD=yes \
-e MYSQL_DATABASE=flasky -e MYSQL_USER=flasky \
-e MYSQL_PASSWORD=1234 \
mysql/mysql-server:5.7
```

## Docker Compose��������
1. ��дCompose�����ļ�docker-compose.yml  
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
2. ����Ӧ��
    `docker-compose up -d --build` ����������Ӧ
3. ֹͣӦ��
    `docker-compose down`