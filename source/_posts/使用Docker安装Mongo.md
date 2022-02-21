---
title: 使用Docker安装Mongo
date: 2021-06-10 15:00:00
tags: 运维
---
环境
- centos:7.9
- docker:1.13.1
- mongo:latest
#### 一、安装docker和docker-compose
```
# 1.使用yum安装docker
yum install -y docker
# 2.安装epel第三方软件包(Extra Packages for Enterprise Linux)
yum install -y epel-release
# 3.安装docker编排
yum install -y docker-compose
```
#### 二、使用docker编排安装mongo
##### compose内容
```
mongodb:
    image: docker.io/mongo
    container_name: mongodb
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/mongo/db:/data/db
      - ./data/mongo/log:/var/log/mongodb
    ports:
      - 27017:27017
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin
      - TZ=Asia/Shanghai
    privileged: true
```
##### 命令
```
# -d 参数为后台执行
docker-compose up -d
```
#### 三、防火墙端口
```
# 1.查看当前开启的端口
firewall-cmd --zone=public --list-ports --permanent
# 2.添加开放永久端口
firewall-cmd --zone=public --add-port=27017/tcp --permanent
# 3.重新加载配置
firewall-cmd --reload
```
#### 四、检查 mongodb 是否运行正常
```
# 1.查看容器运行情况
docker ps
# 2.容器日志查看
docker logs mongo
docker logs -f mongodb 持续打印
docker logs -f --tail 100 mongodb 打印末端100行
docker logs --tail 100 mongodb > mongodb.log 输出日志到当前mongodb.log文件中
```