---
title: WordPress快速安装
date: 2023-03-19 19:19:38
updated: 2023-12-13 19:19:38
tags:
  - Wordpress
---
### 准备

- 一台服务器
- 一个域名

> 本文使用的操作系统为 Ubuntu 22.04

### 一把梭

```
apt update -y && apt upgrade -y
apt-get install htop wget curl git -y
```

### 安装 Docker、Docker-Compose

见 [一键脚本](https://noooy.com/2023/12/e76f8f1a9ce2.html#Docker%E3%80%81Docker-Compose-%E5%AE%98%E6%96%B9%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85)

### 拉取仓库

```
cd /opt/ && git clone https://github.com/nezhar/wordpress-docker-compose.git && cd wordpress-docker-compose
```

### 编辑用户配置

```
cp env.example .env && nano .env
```


修改完后保存并退出

### 启动编排

```
docker-compose up -d
```

### 查看容器运行情况

```
docker ps
```

### 访问 WordPress 安装页面

浏览器输入http://ip:80/

进入WorpPress安装引导

### 使用 phpmyadmin 管理数据库

浏览器输入http://ip:8080/

初始用户名为root，密码为.env配置文件中的密码

### Https 证书

证书一键申请脚本

```
wget -N --no-check-certificate https://raw.githubusercontent.com/CCCOrz/auto-acme/main/acme.sh && bash acme.sh
```