---
title: Obsidian 食用配方
date: 2023-12-15 17:11:44
updated: 2023-12-15 17:11:44
tags:
  - 博客
  - Hexo
  - Obsidian
---
## 前言
1. 不知道什么是 [Obsidian](https://obsidian.md/) 
2. 使用 Obsidian 和 GitHub 实现在各个终端设备同步笔记
## 开始
### 基本设置
1. 客户端可在 `设置` - `About` 中切换为 `简体中文` 
2. `创建新库`或者打开已经 Clone 在本地的代码仓库
### 忽略非关键文件
1. 打开 `设置` - `文件与链接` - `忽略文件`
2. 选择除了 `source` 之外的一些文件夹，如 `node_modules/` 等
>这一步是为了提高软件检索效率
3.  在你的根目录 `.gitignore` 加入下面这行
```
.obsidian/workspace
```
>防止不必要的冗余插件文件提交到 Git 仓库
### 添加 Obsidian 模板
1. 创建模板文件
在 `source` 目录下新增 `_obsidian` 文件夹，在文件夹中创建一个模板文件 `template.md`
```
---
title: {{title}}
date: {{date}}
updated: {{date}}
tags: []
categories: []
---
```
2. 配置软件模板目录
打开 `设置` - `模板` - `模板文件夹位置`，输入
```
source/_obsidian
```
3. 创建新文章之后点击左侧工具栏 `插入模板` 按钮就可以快速生成文章信息
### 使用三方插件
>使用第三方插件首先需要关闭 `设置 - 第三方插件 - 安全模式`
#### Obsidian Git
在 `社区插件市场` 搜索这个插件并安装启用，并在配置项中填入 github 用户名和密码。详细配置见 [obsidian-git](https://github.com/denolehov/obsidian-git)
>推荐使用 Personal access token，具体可见[个人访问令牌](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#keeping-your-personal-access-tokens-secure)
#### File Tree Alternative Plugin
这个插件可以让左侧目录结构更加专注，安装启用即可。其他配置见 [file-tree-alternative](https://github.com/ozntel/file-tree-alternative)
## 在 iPhone 上使用
使用方式和 PC 并无大差异
#### 使用 ISH 克隆仓库到 iOS 本地存储中
略
#### 直接将代码压缩包解压在 Obsidian 库文件中
使用 https 克隆仓库并发送到移动设备上，具体可参考 [Sync on your iOS](https://forum.obsidian.md/t/obsidian-git-sync-on-your-ios-without-any-extra-app/60639) 