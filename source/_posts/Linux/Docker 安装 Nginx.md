---
title: Docker 安装 Nginx
date: 2023-12-15 20:57:40
updated: 2023-12-15 20:57:40
tags:
  - Linux
  - Docker
  - Nginx
---
## Bash
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