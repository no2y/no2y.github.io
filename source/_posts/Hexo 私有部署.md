---
title: Hexo 私有部署
date: 2023-12-20 15:38:00
updated: 2023-12-20 15:38:00
tags:
  - 博客
  - Linux
  - Docker
  - Hexo
categories:
---
## 前言
如果你还没有部署过 Hexo ，可以看看官方手册，如何快速[在 GitHub Pages 上部署 Hexo](https://hexo.io/zh-cn/docs/github-pages)
当部署好之后，部分大陆用户可能会访问不了你的网站。所以一起动手将你的网站发布到你的个人服务器上吧~

准备小料：
- 一台国内能访问的云服务器
- 已部署到 GitHub Pages 的 Hexo 页面
- 一个域名（可选）
## 安装 Nginx 
这里使用 [Docker安装](https://noooy.com/2023/12/8e05ef51573d.html)，请按照流程配置
## 编辑 CI 配置
1. 打开 `.github/workflows/pages.yml`
2. 在步骤  `Upload Pages artifact` 后追加以下内容（注意缩进格式）：
```yaml
	- name: Upload to Personal Server
	if: env.SSH_PRIVATE_KEY && env.SERVER_USER && env.SERVER_ADDRESS && env.SERVER_PORT && env.SERVER_PAGE_PATH
	uses: appleboy/scp-action@v0.1.4
	with:
		host: ${{ vars.SERVER_ADDRESS }}
		username: ${{ vars.SERVER_USER }}
		port: ${{ vars.SERVER_PORT }}
		key: ${{ secrets.SSH_PRIVATE_KEY }}
		source: "./public/*"
		target: ${{ vars.SERVER_PAGE_PATH }}
	env:
		SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
		SERVER_USER: ${{ vars.SERVER_USER }}
		SERVER_ADDRESS: ${{ vars.SERVER_ADDRESS }}
		SERVER_PORT: ${{ vars.SERVER_PORT }}
		SERVER_PAGE_PATH: ${{ vars.SERVER_PAGE_PATH }}
```
3. 在 Hexo GitHub Pages 仓库的 `设置` `Secrets and variables` `Actions` 
	- 添加一个 Secrets `SSH_PRIVATE_KEY` 具体生成方式见 [SSH 秘钥登录](https://noooy.com/2023/06/ccc71c01cfd3.html)
	- 切换到 `Variables` 添加 `Repository variables`
		- SERVER_ADDRESS：服务器地址
		- SERVER_USER：用户名
		- SERVER_PORT：端口
		- SERVER_PAGE_PATH：存放 Hexo 打包产物的路径（如果是用第二点中的方式安装，路径应该是 `/opt/docker_nginx/html/` ）
## 部署
提交 main 分支，会自动部署到你的服务器和GitHub Pages ~