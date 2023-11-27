---
title: PR某次提交
date: 2023-11-27 16:02:47
updated: 2023-11-27 16:02:47
tags: 
categories:
  - git
---
本流程适用于`本地仓库`已经魔改多次，某次又想给`原始仓库`提交合并请求并且不能携带魔改后的代码，记录下这个方式，如有不对请指正。

### Frok 副本 Clone 到本地

```bash
git clone https://github.com/your-github-name/repository-name-fork.git
```

### 原始仓库 Clone 到本地


```bash
original-repository-name=${原始仓库本地名称}

# 添加原始仓库 Remote
git remote add ${original-repository-name} https://github.com/original-owner/repository-name.git

# 查看本地关联仓库
git remote show
# your-github-name
# original-repository-name
# ...

# 获取最新信息
git fetch ${original-repository-name}

# 基于原始仓库新建分支
git checkout -b ${original-repository-name}/new_merge_branch

git branch
# * new_merge_branch
# main

# 查看本地提交日志
git log --oneline
# a123456
# b123456
# ...

# 合并某一次提交
git cherry-pick a123456

# 处理冲突(如果有)

git push origin -u new_merge_branch
```

### 在 GitHub 上创建 Pull Request

1. 选择 `repository-name-fork/new_merge_branch` -> `original-owner/repository-name:main`
2. 书写 Commit 信息
3. 耐心等待 `original-owner` 审核