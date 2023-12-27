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
在域名托管商处增加以下 DNS 记录

| DNS Record Type | Hostname                                 | Record Type | Value                                       |
|-----------------|------------------------------------------|-------------|---------------------------------------------|
| PTR             | mail.your-domain.com                     | PTR         | (IP address of your server)                 |
| A               | mail.your-domain.com                     | A           | 1.2.3.4 (your IP)                           |
| CNAME           | smtp.your-domain.com                     | CNAME       | mail.your-domain.com                        |
| CNAME           | pop.your-domain.com                      | CNAME       | mail.your-domain.com                        |
| CNAME           | imap.your-domain.com                     | CNAME       | mail.your-domain.com                        |
| MX              | your-domain.com                          | MX          | mail.your-domain.com                        |
| SPF             | your-domain.com                          | TXT         | "v=spf1 mx ~all"                            |
| DKIM            | _s20160910378._domainkey.your-domain.com | TXT         | "k=rsa; p=[Your DKIM Key]"                  |
| DMARC           | _dmarc.our-domain.com                    | TXT         | "v=DMARC1; p=none; rua=mailto:[email address]" |

此表包含设置 Poste.io 邮件服务器所需的 DNS 记录，例如 PTR、A、CNAME、MX、SPF、DKIM 和 DMARC 记录。将 `your-domain.com` 、 `1.2.3.4` 、 `[Your DKIM Key]` 和 `[email address]` 替换为 DMARC 报告的特定域、IP 地址、DKIM 密钥和电子邮件地址​​。
更多详细查看 [官网 DNS 配置](https://poste.io/doc/configuring-dns)
>PTR 记录一般需要找你的 VPS 提供商协助添加
## 编写 docker-compose
1. 为了好管理配置，将配置项整理成 docker-compose
2. 根据 [官方 Docker 安装](https://poste.io/doc/getting-started)手册，拜托 GPT 输出一份 docker-compose.yml
```yml
version: '3.8'
services:
  poste.io:
    image: analogic/poste.io # Use poste.io/mailserver for PRO version
    container_name: mailserver
    hostname: mail.npy.icu
    environment:
      - TZ=Asia/Shanghai # Timezone settings
      - HTTPS=OFF # Optional: Disable HTTPS redirects (useful for reverse proxy setups)
    volumes:
      - ./data:/data # Mount data directory
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
    ports:
      - "25:25" # SMTP
      - "80:80" # HTTP
      - "110:110" # POP3
      - "143:143" # IMAP
      - "443:443" # HTTPS
      - "465:465" # SMTPS
      - "587:587" # MSA
      - "993:993" # IMAPS
      - "995:995" # POP3S
      - "4190:4190" # Sieve
```
3. 将文件拷贝到你要运行邮件服务的 VPS，运行 `docker compose up -d`（旧版本命令是 `docker-compose up -d`）。如果还没有安装 Docker compose，请查看[一键安装命令](https://noooy.com/2023/12/e76f8f1a9ce2.html#Docker%E3%80%81Docker-Compose-%E5%AE%98%E6%96%B9%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85)
## 后台配置
1. 浏览器打开 `mail.your-domain.com:80`
2. 按步骤设置