---
title: poste.io 搭建邮件服务
date: 2023-12-24 17:38:45
updated: 2023-12-24 17:38:45
tags:
  - Linux
categories:
  - 教程
---
## 准备
1. 一个能使用 `25` 端口的服务器
2. 一个域名
## 关于 poste.io
[官网](https://poste.io/)
官网介绍：
1. SMTP + IMAP + POP3 + 反垃圾邮件 + 防病毒  
2. 网络管理+网络电子邮件  
3. 5 分钟内快速上手
## DNS 配置
我的域名托管在 Cloudflare，增加以下 DNS 记录

|类型|名称|内容|
|---|---|---|
|A|mail|111.222.333.444|
|MX|your-domain.com|mail.your-domain.com|
|TXT|your-domain.com|v=spf1 mx ~all|
|TXT|aaa|bbb|
|TXT|ccc|ddd|
[官网 DNS 配置](https://poste.io/doc/configuring-dns)
## Docker 安装 poste.io
```bash
docker run \
    --net=host \
    -e TZ=Europe/Prague \
    -v /your-data-dir/data:/data \
    --name "mailserver" \
    -h "mail.example.com" \
    -t analogic/poste.io
```
[更多的配置项](https://poste.io/doc/getting-started)
## 编写 docker-compose
为了好管理配置，将配置项整理成 docker-compose

。。待补充

