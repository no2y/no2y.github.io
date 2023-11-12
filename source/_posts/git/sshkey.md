---
title: 多个GitHub账户的SSH密钥配置指南
categories:
  - git
date: 2023-11-12 15:54:21
updated: 2023-11-12 15:56:00
---

## 1. 生成SSH密钥
对于每个GitHub账户，使用以下命令生成一个新的SSH密钥对：
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/id_rsa_account1
ssh-keygen -t rsa -b 4096 -C "your_other_email@example.com" -f ~/.ssh/id_rsa_account2
```

## 2. 添加公钥到GitHub账户
- 登录到GitHub账户。
- 导航到 **Settings** -> **SSH and GPG keys**。
- 点击 **New SSH key** 按钮，将生成的公钥（例如 `~/.ssh/id_rsa_account1.pub`）内容粘贴进去。

## 3. 配置SSH配置文件
编辑 `~/.ssh/config` 文件，为每个账户添加以下配置块：
```bash
# GitHub Account 1
Host account1
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_account1

# GitHub Account 2
Host account2
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_account2
```

## 4. 启动SSH Agent
在终端中运行以下命令来启动 `ssh-agent`：
```bash
eval "$(ssh-agent -s)"
```

## 5. 将SSH密钥添加到SSH Agent
使用 `ssh-add` 命令将每个账户的私钥添加到 `ssh-agent`：
```bash
ssh-add ~/.ssh/id_rsa_account1
ssh-add ~/.ssh/id_rsa_account2
```

## 6. 测试SSH连接
测试是否能够通过SSH连接到GitHub：
```bash
ssh -T git@account1
ssh -T git@account2
```

## 7. 使用SSH克隆仓库
使用配置文件中设置的别名来克隆GitHub仓库：
```bash
git clone git@account1:user/Repo1.git
git clone git@account2:otheruser/Repo2.git
```
如果是已有仓库：
```bash
git remote -v
```
看看当前关联的地址：
```bash
origin  git@github.com:no2y/no2y.github.io.git (fetch)
origin  git@github.com:no2y/no2y.github.io.git (push)
```
将其修改为刚刚 ~/.ssh/config 中配置的 Host
```bash
git remote set-url origin git@account1:no2y/no2y.github.io.git
```

## 8. 配置Git用户名和电子邮件
为每个仓库配置正确的Git用户名和电子邮件地址：
```bash
git config user.name "Your Name"
git config user.email "your_email@example.com"
```

或者为所有仓库全局设置用户名和电子邮件：
```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```
