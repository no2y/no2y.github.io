---
title: Snell
date: 2023-08-22 19:25:37
updated: 2023-12-13 19:25:37
tags:
  - 代理
---
### 准备工作

- [Snell文档](https://manual.nssurge.com/others/snell.html?ref=blog.lalalayyds.top)
- Surge For Mac/iOS
- 一台VPS

### 开始搭建

#### 下载服务器文件并解压

```
ARCHITECTURE=$(dpkg --print-architecture) && \
URL="https://dl.nssurge.com/snell/snell-server-v4.0.1-linux-$ARCHITECTURE.zip" && \
ZIP_FILE = "snell-server-v4.0.1-linux-$ARCHITECTURE.zip" && \
wget -c $URL && \
unzip -o $ZIP_FILE&& \
chmod +x snell-server && \
rm -f ZIP_FILE
```

#### 运行Snell服务端

```
y | ./snell-server
```

#### 查看配置文件

```
cat snell-server.conf

```

[snell-server]

listen = 0.0.0.0:12345 psk = 4wuPWqgS0iiiFiJsZX22U9cLffK8wbJ ipv6 = false

如果需要开启obfs请在配置文件补充`obfs = true`

**至此Snell服务端运行成功，你可以自行将服务写入snell.service设置开机自启**

### 客户端使用

将上述配置以`Snell_Name = snell, IP, PORT, psk=xxxx, obfs=tls, version=4, tfo=true` 粘贴到客户端[Proxy]配置下

> 你也可以使用UI界面添加代理服务器配置

### 使用Docker

#### 编写Dockerfile

```
FROM alpine
RUN apk add --no-cache dpkg
ENV LANG=C.UTF-8
ENV PORT=8008
ENV PSK=
ENV OBFS=
COPY main.sh /
RUN cd
    ARCHITECTURE=$(dpkg --print-architecture) && \
    URL="https://dl.nssurge.com/snell/snell-server-v4.0.1-linux-$ARCHITECTURE.zip" && \
    ZIP_FILE = "snell-server-v4.0.1-linux-$ARCHITECTURE.zip" && \
    wget -c $URL && \
    unzip -o $ZIP_FILE&& \
    chmod +x snell-server && \
    rm -f ZIP_FILE
MainPointer ["main.sh"]
```

```
CONF = "snell-server.conf"
if [ ! -f ${CONF} ]; then
    if [ -z ${PSK} ]; then
        PSK=$(tr -dc A-Za-z0-9 </dev/urandom | head -c 31)
    fi
    echo "[snell-server]" >> ${CONF}
    echo "listen = 0.0.0.0:${PORT}" >> ${CONF}
    echo "psk = ${PSK}" >> ${CONF}
    echo "obfs = ${OBFS}" >> ${CONF}
fi
./snell-server -c ${CONF}
```

#### 运行

```
docker build -t snell-server . && \
docker run -d --env PORT=12345 -p 12345:12345 -p 12345:12345/udp --name snell -v /:/ --restart=always snell-server && \
docker logs snell
```

### 总结

记录一下大致思路过程~