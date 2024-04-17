---
title: Docker 安装 Nginx
date: 2023-12-15 20:57:40
updated: 2023-12-15 20:57:40
tags:
  - Linux
  - Docker
  - Nginx
---
## 脚本安装
1. 需要安装docker 和 docker compose 最新版
2. 空闲的 80 端口
3.  新建 nginx_docker_install.sh 写入以下内容执行
```bash
#!/bin/bash

# 定义目标目录变量
target_dir="/opt/nginx_docker"

# 第一阶段：提取默认配置和HTML文件
echo "启动临时Nginx容器来提取默认配置和HTML文件..."
docker run --name tmp-nginx -d nginx:latest

# 等待几秒以确保Nginx容器已经完全启动
sleep 5

echo "从临时容器复制默认配置和HTML文件..."
mkdir -p ${target_dir}
docker cp tmp-nginx:/etc/nginx/ ${target_dir}/
docker cp tmp-nginx:/usr/share/nginx/html ${target_dir}/

echo "停止并移除临时Nginx容器..."
docker stop tmp-nginx && docker rm tmp-nginx

# 检查docker-compose.yml文件是否存在
compose_file="$(pwd)/docker-compose.yml"
if [ ! -f "$compose_file" ]; then
    echo "docker-compose.yml文件不存在，正在创建..."
    cat << EOF > docker-compose.yml
version: '3.7'

services:
  nginx:
    image: nginx:latest
    container_name: nginx_docker
    network_mode: host
    volumes:
      - ${target_dir}/nginx/:/etc/nginx/
      - ${target_dir}/html/:/usr/share/nginx/html
    restart: always
EOF
fi

# 第二阶段：使用docker-compose启动定制的Nginx容器
echo "使用docker-compose启动定制的Nginx容器..."
docker-compose up -d
mv docker-compose.yml ${target_dir}/
echo "完成。"

```
## 安装
```bash
docker pull nginx:latest && \
docker run --name nginx -p 80:80 -d nginx && \
docker cp nginx:/etc/nginx/ /opt/nginx_docker/ && \
docker cp nginx:/usr/share/nginx/html /opt/nginx_docker/ && \
docker stop nginx && docker rm nginx && \
docker run -p 80:80 --name nginx --net=host --restart=always \
-v /opt/nginx_docker/:/etc/nginx/ \
-v /opt/nginx_docker/html:/usr/share/nginx/html \
-d nginx
```
## 说明
1. 主配置文件 `/opt/nginx_docker/nginx.conf*`
2. 子配置文件 `/opt/nginx_docker/conf.d/*`
3. 静态资源路径 `/opt/nginx_docker/html/*`
**注意配置文件中路径写法**
例：
站点文件存放于 `/opt/nginx_docker/html/blog/`
```bash
ls /opt/nginx_docker/html/blog/
index.html style.css ...
```
SSL证书存放于 `/opt/nginx_docker/ssl/aaa.com/`
```bash
ls /opt/nginx_docker/ssl/aaa.com/
fullchain.pem private_key.pem
```
对应的 Nginx 配置文件如下：
`/opt/nginx_docker/conf.d/aaa.conf`
```nginx
server {
    listen 443 ssl http2;
    server_name aaa.com;
    ssl_certificate      /etc/nginx/ssl/aaa.com/fullchain.pem;
    ssl_certificate_key  /etc/nginx/ssl/aaa.com/private_key.pem;
    root /usr/share/nginx/html/blog/;
    index index.html;
    location / {
        try_files $uri $uri/ =404;
    }
}
```
>注意：配置中的资源路径、SSL证书路径为 Docker 容器中的**虚拟路径**而非宿主机路径