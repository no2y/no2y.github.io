---
title: Git 常用操作
date: 2023-12-19 16:58:49
updated: 2023-12-19 16:58:49
tags:
---
## 修改最近一次提交信息
确保没有未提交的更改，运行以下命令
```bash
git commit --amend
```
编辑后保存并关闭编辑器，运行以下命令
```bash
git push --force-with-lease
```
## 恢复误删提交记录
查看操作记录
```bash
git reflog
aabbcc1 (HEAD -> main, origin/main) HEAD@{0}: rebase (finish): returning to xxx
aabbcc1 (HEAD -> main, origin/main) HEAD@{0}: rebase (start): checkout HEAD~1
aabbcc2 HEAD@{1}: commit: xxx
aabbcc3 HEAD@{2}: commit: xxx
aabbcc4 HEAD@{3}: commit: xxx
```
恢复记录
```bash
git reset --hard aabbcc2
```
## .gitignore 修复
1. 文件 aaa 已经推送到远端
2. .gitignore 添加 aaa/** 不生效
按以下步骤：
1. .gitignore 添加 aaa/**
2. git add .gitignore
3. git commit -m "update .gitignore"
4. git rm -r --cached aaa
5. git add .
6. git commit -m "fix .gitignore"