---
title: 使用 Docker 安装 Mysql80
date: 2021-06-10 14:03:00
tags: [运维,部署]
categories: [运维]
---
### 使用 Docker 安装 Mysql80
环境：
- centos:7.9
- docker:1.13.1
- mysql80:latest

#### 一、安装 docker 和 docker-compose
```
# 1.使用yum安装docker
yum install -y docker
# 2.安装epel第三方软件包(Extra Packages for Enterprise Linux)
yum install -y epel-release
# 3.安装docker编排
yum install -y docker-compose
```
#### 二、使用 docker 编排安装 mysql80
##### compose 内容
```
version: '3'
services:
  mysql:
    restart: always
    image: docker.io/mysql
    container_name: mysql80
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/mysql/mydir:/mydir
      - ./data/mysql/datadir:/var/lib/mysql
      - ./data/mysql/:/etc/my.cnf
      #      数据库还原目录 可将需要还原的sql文件放在这里
      - ./data/mysql/source:/docker-entrypoint-initdb.d
    environment:
      - "MYSQL_ROOT_PASSWORD=6yhn^YHN"
      - "TZ=Asia/Shanghai"
    ports:
      - 3306:3306
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
firewall-cmd --zone=public --add-port=3306/tcp --permanent
# 3.重新加载配置
firewall-cmd --reload
```
#### 四、检查 mysql 是否运行正常
```
# 1.查看容器运行情况
docker ps
# 2.容器日志查看
docker logs mysql80
docker logs -f mysql80 持续打印
docker logs -f --tail 100 mysql80 打印末端100行
docker logs --tail 100 mysql80 > mysql.log 输出日志到当前mysql.log文件中
```