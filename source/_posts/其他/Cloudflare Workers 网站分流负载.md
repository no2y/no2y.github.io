---
title: Cloudflare Workers 网站分流负载
date: 2023-12-20 14:14:08
updated: 2023-12-20 14:14:08
tags: 
categories:
---
## 前言
本博客目前是托管在 Github Pages 上，突发奇想部署一份在私人服务器上，并且两边资源都能够动态访问到。于是了解到几种来分流资源的策略：
- DNS 轮询
- 开源平台 Nginx、Apache 等
- 各大云商提供的负载器服务等
- CND
起初想尝试 Cloudflare 平台的智能 DNS 服务，但是需要付费（$5/mo）。于是就想通过 Cloudflare Works 来尝试一下。
## 注册 Cloudflare 账户
1. 前往 [Cloudflare](https://cloudflare.com/) 注册开通账户，并将域名托管至 CF
2. 添加 CNAME 记录解析至 YourGithubRepoName.github.io
3. 添加 A 记录
4. SSL/TLS 开启完全严格模式
5. 开启 DNS 记录中的 Proxy （小橘云）
## 创建 Workers
1. 转到 Workers 和 Pages
2. 选择 `创建应用程序`
3. 贴上以下代码
```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

// 轮询的目标服务
const TARGETS = [
  'aaa.com',
  'bbb.com'
]

async function handleRequest(request) {
  const url = new URL(request.url)
  url.hostname = getRandomServer()
  return await fetch(url, request)
}

// 每小时切换不同的目标
function getServerEveryHour() {
  const d = new Date()
  const day = d.getHours()
  return TARGETS[day%TARGETS.length]
}

// 每次都随机目标（要注意目标之间是否有跨域）
function getRandomServer() {
  return TARGETS[Math.floor(Math.random() * TARGETS.length)]
}
```
4. 保存并部署
## 使用 Workers
1. 查看刚刚创建的 Worker，详情页选择触发器
2. 在 `路由` 界面点击 `添加路由`
	- 路由：可以填 `aaa.com` ，可以使用通配符。[具体可见](https://developers.cloudflare.com/workers/configuration/routing/routes/#matching-behavior)
	- 选择区域：刚才创建并