---
title: 使用Docker安装ActiveMq
date: 2021-06-22 16:25:00
tags: 运维
---
环境：
- centos:7.9
- docker:1.13.1
- ActiveMq:latest

#### 一、安装 docker 和 docker-compose
```
# 1.使用yum安装docker
yum install -y docker
# 2.安装epel第三方软件包(Extra Packages for Enterprise Linux)
yum install -y epel-release
# 3.安装docker编排
yum install -y docker-compose
```
#### 二、使用 docker 编排安装 ActiveMq
##### compose 内容
```
version: '3'
services:
  activemq-master:
    container_name: activemq-master
    #使用的镜像
    image: webcenter/activemq
    restart: always
    #容器的映射端口
    ports:
      - 61616:61616
      - 8161:8161
    #定义挂载点
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/activemq/data:/data/activemq
      - ./data/activemq/log:/var/log/activemq
    #环境变量
    environment:
      - ACTIVEMQ_ADMIN_LOGIN=admin
      - ACTIVEMQ_ADMIN_PASSWORD=admin
      - ACTIVEMQ_CONFIG_MINMEMORY=512
      - ACTIVEMQ_CONFIG_MAXMEMORY=2048
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
firewall-cmd --zone=public --add-port=8161/tcp --permanent
firewall-cmd --zone=public --add-port=61616/tcp --permanent
# 3.重新加载配置
firewall-cmd --reload
```
#### 四、检查 ActiveMq 是否运行正常
```
# 1.查看容器运行情况
docker ps
# 2.容器日志查看
docker logs activemq-master
docker logs -f activemq-master 持续打印
docker logs -f --tail 100 activemq-master 打印末端100行
docker logs --tail 100 activemq-master > activemq.log 输出日志到当前 activemq.log 文件中
# 3.打开 http://localhost:8161/admin 查看 activemq 管理后台
```
