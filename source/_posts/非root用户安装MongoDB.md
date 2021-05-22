---
title: 非root用户安装MongoDB
urlname: Install-MongoDB
tags: [dev, intro]
date: 2018-07-31 10:59:21
updated: 2018-07-31 10:59:21
---
在实验室的服务器上安装MongoDB，然而没有sudo权限，只能安装已编译的程序了。
步骤如下：

1. 去[官网](https://www.mongodb.com/download-center#community)查看下载并解压最新版4.0.0的MongoDB包
<!--more-->
```bash
$ wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1404-4.0.0.tgz
$ tar -zxvf mongodb-linux-x86_64-ubuntu1404-4.0.0.tgz
```

2. 官网说需要安装OpenSSL
>The binary of this version has been compiled with SSL enabled and dynamically linked. This requires that SSL libraries be installed separately. See here for more information on installing OpenSSL.
安装方法参考：[这个](https://skynineblog.wordpress.com/2016/04/08/linux%E7%B3%BB%E7%BB%9F%E5%AE%89%E8%A3%85-openssl%E4%B8%A4%E7%A7%8D%E6%96%B9%E6%B3%95/)

3. 将路径添加到PATH
```bash
$ export PATH="/path/to/dir/:$PATH"
$ source ~/.bashrc
```

4. 在合适位置新建`mongod.conf`配置文件，主要是设置db和log文件夹，以及`fork`（后台运行），参考配置：
```text
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /home/duhaihua/mongo/db
  journal:
    enabled: true

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /home/duhaihua/mongo/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0

# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo
  fork: true

# security.
security:
  authorization: enabled
```

5. 启动mongod服务
```bash
mongod --config ~/mongo/mongod.conf
```
可以用以下命令查看mongod是否已启动：
```shell
$ ps -ef|grep mogod
```

6. 打开mongo shell，使用mongoDB
```shell
mongo
```