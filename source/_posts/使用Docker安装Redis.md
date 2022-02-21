---
title: 使用 Docker 安装 Redis
date: 2021-06-10 15:05:00
tags: 运维
---
### 使用 Docker 安装 Redis
环境：
- centos:7.9 
- docker:1.13.1
- redis:latest

#### 一、安装 docker 和 docker-compose
```
# 1.使用yum安装docker
yum install -y docker
# 2.安装epel第三方软件包(Extra Packages for Enterprise Linux)
yum install -y epel-release
# 3.安装docker编排
yum install -y docker-compose
```
#### 二、使用 docker 编排安装 redis
##### compose 内容
```
version: '3'
services:
  redis:
    image: docker.io/redis
    container_name: redis
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/redis/data:/data
      - ./data/redis/redis.conf:/etc/redis.conf
      - ./data/redis/log:/var/log/redis
    command: redis-server /etc/redis.conf
    ports:
      - 6379:6379
    environment:
      - TZ=Asia/Shanghai
    privileged: true
```
##### 命令
```
# -d 参数为后台执行
docker-compose up -d
```
##### 配置 redis.conf 文件
```
# 对外开放访问
bind 0.0.0.0
# 开启aof模式
appendonly yes
# 使用每次更新都写到磁盘中
appendfsync always
```
把配置文件放入 ./data/redis/ 目录中
再使用 docker-compose 重启 redis 容器
#### 三、防火墙端口
```
# 1.查看当前开启的端口
firewall-cmd --zone=public --list-ports --permanent
# 2.添加开放永久端口
firewall-cmd --zone=public --add-port=6379/tcp --permanent
# 3.重新加载配置
firewall-cmd --reload
```
#### 四、检查 redis 是否运行正常
```
# 1.查看容器运行情况
docker ps
# 2.容器日志查看
docker logs redis
docker logs -f redis 持续打印
docker logs -f --tail 100 redis 打印末端100行
docker logs --tail 100 redis > redis.log 输出日志到当前 redis.log文件中
```