---
title: "Tx"
date: 2023-05-10T13:48:10+08:00
draft: false
categories:
- 服务器
---
# 配置腾讯服务器(本机

主要是配置了静态网站.

其他一些安装的东西:

https://github.com/svenstaro/miniserve 文件服务器  基于rust 因为glibc版本bug,先搁置

默认安装了apache2,占用了80端口,我暂时用

sudo systemctl stop apache2 将其关闭

https://github.com/static-web-server/static-web-server  静态网站服务器,基于rust

文档链接:https://static-web-server.net

创建了一个服务:

```apache
#以下内容询问ChatGPT老师得到的结果,亲测可用
sudo vim /etc/systemd/system/micsws.service
#保存并关闭文件后，重新加载systemd守护进程，以便它可以识别新的服务文件。可以使用以下命令来重新加载：
sudo systemctl daemon-reload
#启动服务：
sudo systemctl start micsws
#可以使用以下命令检查服务是否正在运行：
sudo systemctl status micsws
#如果服务正在运行，输出将会显示“active (running)”；否则，将会显示“inactive (dead)”。
#将服务添加到开机自启动项：
sudo systemctl enable micsws
#这样，当计算机启动时，服务将自动启动。


[Unit]
Description=sws for mic 
After=network.target

[Service]
StartLimitIntervalSec=0
StartLimitBurst=5
#这个软件要sudo权限
User=sudo
WorkingDirectory=/home/dzmfg/myblog/
ExecStart=/home/dzmfg/tools/static-web-server -w /home/ubuntu/.config/sws/config.toml
Restart=always
RestartSec=1

[Install]
WantedBy=multi-user.target

```

配置文件设置如下:

后面的http到https的重定向还没做好 日.没搞懂

```toml
[general]
host = "::"
port = 443
root = "/home/ubuntu/myblog"
log-level="info"
http2=true
http2-tls-cert = "/home/ubuntu/.config/sws/certificate.crt"
http2-tls-key = "/home/ubuntu/.config/sws/private.key"
[advanced]

### URL Redirects

[[advanced.redirects]]
source = "http://*"
destination = "https://"
kind = 301
```
