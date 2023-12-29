---
title: Mac 下管理 Python 版本
date: 2023-12-17
updated: 2023-12-17
tags:
  - Mac
  - Python
---
## 安装 Homebrew
[安装地址](https://docs.brew.sh/Installation)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```
安装完了看看版本
```bash
brew -v
```
>注意需要终端能够连接 Github
## 安装 Pyenv
```bash
brew update 
brew install pyenv
pyenv -v
```
## 使用 Pyenv 管理 Python 版本
### 安装 Python
```bash
pyenv install 3.10.12
pyenv versions
pyenv global 3.10.12
```
### Pyenv 常用命令
```bash
使用方式: pyenv <命令> [<参数>]

命令:
  commands    查看所有命令
  local       设置或显示本地的Python版本
  global      设置或显示全局Python版本
  shell       设置或显示shell指定的Python版本
  install     安装指定Python版本
  uninstall   卸载指定Python版本)
  version     显示当前的Python版本及其本地路径
  versions    查看所有已经安装的版本
  which       显示安装路径
```
## 使用 Python
这个时候终端输入 `python --version` ，可能会显示：
```bash
zsh: command not found: python
```
### 修改 zsh 配置
`nano ~/.zshrc`
增加以下配置：
```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/shims:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
 eval "$(pyenv init -)"
fi
```
最后，输入 `source ~/.zshrc` 重载配置
## 结语
至此，你本地 Mac 就可以通过  Pyenv 切换不同的 Python 版本了