---
title: 使用RClone轻松搭建WebDAV服务器，实现手机电脑共享文件
---
使用RClone轻松搭建WebDAV服务器，实现手机电脑共享文件

### RClone安装
``` shell
curl https://rclone.org/install.sh | sudo bash
```


### 启动WebDav
其中/simonpool为本地目录
--user simon simon为用户名
--pass simon 这里的simon为密码

``` shell
rclone serve webdav --addr :8889 /simonpool --cache-dir /cache --user simon --pass simon  --no-checksum  --no-modtime  --no-seek
```

### pm2启动webdav
pm2是一款linux上的进和管理工具，可以作为守护进程使用。当用pm2启动一个linux进程后，如果该进程出现崩溃，pm2可以自动重启该进程，提高可用性。
``` shell
curl -sL https://deb.nodesource.com/setup_16.x | bash -
npm i -g pm2
pm2 start --name webdav rclone  -- serve webdav --addr :8889 /simonpool --cache-dir /cache  --user simon --pass simon  --no-checksum  --no-modtime
```


### pm2启动RClone的http服务，做一个简单的文件服务器
``` shell
curl -sL https://deb.nodesource.com/setup_16.x | bash -
npm i -g pm2
pm2 start --name http rclone -- serve http --addr :8888 /simonpool
```