---
title: SSH 秘钥登录
date: 2023-06-23 18:24:22
updated: 2023-09-13T21:26:24
tags:
  - Linux
categories:
  - 随记
---
## 生成密钥对
```bash
ssh-keygen -t rsa -b 4096
```
生成的密钥对默认保存在`~/.ssh`目录下，私钥为`id_rsa`，公钥为`id_rsa.pub`
## 配置公钥
查看公钥
```bash
cat ~/.ssh/id_rsa.pub
```
配置公钥
```bash
(echo && cat ~/.ssh/id_rsa.pub) >> ~/.ssh/authorized_keys
```
或者登录到服务器，编辑`~/.ssh/authorized_keys`文件，将公钥内容粘贴到该文件中
## 配置 SSH 服务
登录到服务器，编辑SSH配置文件：
`sudo nano /etc/ssh/sshd_config`
确保以下行是这样设置的（不存在则添加）：
```bash
PubkeyAuthentication yes 
```
完成编辑后，保存文件并重启SSH服务以应用更改：
`sudo systemctl restart sshd`
或者在某些系统中，可能需要使用以下命令：
`sudo service ssh restart`
## 验证密钥登录
尝试从客户端使用SSH密钥登录服务器：
`ssh user@server_ip`
如果为私钥设置了密码，系统会提示输入私钥密码。