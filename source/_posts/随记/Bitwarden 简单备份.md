---
title: Bitwarden 简单备份
date: 2024-09-09 13:06:38
updated: 2024-09-09 13:06:38
tags:
  - Bitwarden
---
>发送邮件可以参考[定时备份`MySQL`](/2023/01/6ff369925717.html)这篇文章
### 安装
```bash
apt update && apt-get update \
apt install msmtp -y \ 
apt install mutt -y
```
> Ubuntu 可能还要安装 `apt install sendmail`
### 备份脚本
填写配置就好，建议都写绝对路径。不喜欢`zip`可以换成`tar`。
```bash
#!/bin/bash

##### 配置Begin #####
# 目标文件路径
TARGET_PATH=/path/to/bitwarden-data
# 备份保存路径
BACKUP_PATH=/path/to/backup
# 收件人邮箱
RECV_EMAIL=123456@gmail.com
##### 配置End #####

# 当前时间
CURRENT_TIME=$(date +%Y%m%d_%H%M%S)
# 备份文件后缀
BACKUP_FILE_SUFFIX=_bitwarden_data.zip
# 备份后的文件名称
TAR_FILE_NAME=${CURRENT_TIME}${BACKUP_FILE_SUFFIX}

if ! command -v zip &> /dev/null
then
    echo "zip 未安装，请先安装 zip。"
    exit
fi

[ ! -d "$BACKUP_PATH" ] && mkdir -p "$BACKUP_PATH"
FILE_GZ=${BACKUP_PATH}/${TAR_FILE_NAME}

# 压缩
zip -q -r $TAR_FILE_NAME $TARGET_PATH

EMAIL_TITLE="Bitwarden_备份_$CURRENT_TIME"
# 发送邮件
echo "$EMAIL_TITLE" | mutt -s "$TAR_FILE_NAME" $RECV_EMAIL -a $FILE_GZ

# 删除 7 天以前的备份 「注意写法」
cd $BACKUP_PATH
find $BACKUP_PATH -mtime +7 -name "*${BACKUP_FILE_SUFFIX}"  -exec rm -f {} \;

# 日志
# echo -e "$EMAIL_TITLE 成功\n" >> $BACKUP_PATH/log
```
### 定时任务
每天10:00执行一次
```bash
crontab -e
0 10 * * * bash /path/to/backup.sh
```