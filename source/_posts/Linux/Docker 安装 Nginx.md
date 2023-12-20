---
title: Docker 安装 Nginx
date: 2023-12-15 20:57:40
updated: 2023-12-15 20:57:40
tags:
  - Linux
  - Docker
  - Nginx
---
## 安装
```bash
docker pull nginx:latest && \
docker run --name nginx -p 80:80 -d nginx && \
docker cp nginx:/etc/nginx/ /opt/docker_nginx/ && \
docker cp nginx:/usr/share/nginx/html /opt/docker_nginx/ && \
docker stop nginx && docker rm nginx && \
docker run -p 80:80 --name nginx --net=host --restart=always \
-v /opt/docker_nginx/:/etc/nginx/ \
-v /opt/docker_nginx/html:/usr/share/nginx/html \
-d nginx
```
## 说明
1. 主配置文件 `/opt/docker_nginx/nginx.conf*`
2. 子配置文件 `/opt/docker_nginx/conf.d/*`
3. 静态资源路径 `/opt/docker_nginx/html/*`
**注意配置文件中路径写法**
例：
站点文件存放于 `/opt/docker_nginx/html/blog/`
```bash
ls /opt/docker_nginx/html/blog/
index.html style.css ...
```
SSL证书存放于 `/opt/docker_nginx/ssl/aaa.com/`
```bash
ls /opt/docker_nginx/ssl/aaa.com/
fullchain.pem private_key.pem
```
对应的 Nginx 配置文件如下：
``/opt/docker_nginx/conf.d/aaa.conf`
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