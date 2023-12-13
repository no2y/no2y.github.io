---
title: 定时备份MySQL并发送到邮箱
date: 2023-12-13 19:17:35
updated: 2023-12-13 19:17:35
tags:
  - Linux
---
### 环境

Ubuntu 22.04

### 安装所需软件

> ```
> apt update && apt-get update \
> apt install msmtp -y \ 
> apt install mutt -y
> ```

### 配置文件

vim /etc/Muttrc

```
set sendmail="/usr/bin/msmtp"
set use_from=yes
set realname="163备份邮箱"
set from="source_xxx@163.com"
set envelope_from=yes
```

vim /etc/msmtprc

```
account default
host smtp.163.com
from source_xxx@163.com
auth plain
user source_xxx@163.com
password xxx # 163 邮箱的客户端授权密码
```

### 测试邮件发送

echo "邮件标题" | mutt -s "邮件内容" target_xxx@xxx.com -a 附件.xxx

### 编写脚本

```
#!/bin/bash

##### 配置Begin #####
#备份保存路径
BACKUP_PATH=/data/backup/mysql
#当前时间
CURRENT_TIME=$(date +%Y%m%d_%H%M%S)
#收件人邮箱
RECV_EMAIL=xxx
#数据库地址
HOST=localhost
#数据库用户名
DB_USER=root
#数据库密码
DB_PW=xxx
# 要备份的数据库名
DATABASE=xxx
##### 配置End #####

[ ! -d "$BACKUP_PATH" ] && mkdir -p "$BACKUP_PATH"
FILE_GZ=${BACKUP_PATH}/$CURRENT_TIME.$DATABASE.sql.gz
/usr/local/bin/mysqldump -u${DB_USER} -p${DB_PW} --host=$HOST -q -R --databases $DATABASE  | gzip > $FILE_GZ # 此处必须要用绝对路径

# 所有数据库
#mysqldump --all-databases -xxxxx

echo "数据库备份--$FILE_GZ" | mutt -s "$DATABASE备份" $RECV_EMAIL -a $FILE_GZ

# 删除 7 天以前的备份 「注意写法」
cd $BACKUP_PATH
find $BACKUP_PATH -mtime +7 -name "*sql.gz"  -exec rm -f {} \;
```

### 新建定时任务

每周一0点0分执行一次备份脚本

```
crontab -e
0 0 * * 1 bash /xxx/xxx/backup.sh
```

### 参考

[MySQL 自动备份并发送到邮箱](https://learnku.com/articles/13342/mysql-auto-backup-and-send-to-mailbox)

[将mysql数据备份定时发送到email](https://l1905.github.io/%E8%BF%90%E7%BB%B4/2020/06/05/mysql_backup_by_email/)