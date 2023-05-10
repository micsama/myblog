---
title: "Dedipath"
date: 2023-05-09T23:36:47+08:00
draft: false
---
**Dedipath**
目前主要的需要安装和配置的工作如下:

已安装:

- ranger
- htop

初始化配置等操作:

```bash
#目标机器 先配置用户 刷新apt
adduser dzmfg
adduser dzmfg sudo
apt update
apt install curl
#登陆为dzmfg后 下载安装ssr
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
cd ~
mkdir .local
mkdir .local/bin
mkdir tools
mkdir tools/ssr
cd tools/ssr
wget https://github.com/shadowsocks/shadowsocks-rust/releases/download/v1.15.3/shadowsocks-v1.15.3.x86_64-unknown-linux-gnu.tar.xz 
#然后再解压 这里只需要用到ssserver 这一个二进制文件.
```

其中,再写ssr的配置文件:

```json
{
    "server":"0.0.0.0",
    "server_port":12388,
    "password":"4QPeqOn2nAHUFEQxzSysFe/ax54z5zPa+1hGnhsKxXE=",
    "timeout":3000,
    "method":"chacha20-ietf-poly1305",
    "nameserver":"114.114.114.114"
}
```

接下来安按照以下内容设置守护进程和开机自启:

```bash
#以下内容询问ChatGPT老师得到的结果,亲测可用
sudo vim /etc/systemd/system/micssr.service
#保存并关闭文件后，重新加载systemd守护进程，以便它可以识别新的服务文件。可以使用以下命令来重新加载：
sudo systemctl daemon-reload
#启动服务：
sudo systemctl start micssr
#可以使用以下命令检查服务是否正在运行：
sudo systemctl status micssr
#如果服务正在运行，输出将会显示“active (running)”；否则，将会显示“inactive (dead)”。
#将服务添加到开机自启动项：
sudo systemctl enable micssr
#这样，当计算机启动时，服务将自动启动。
```

其中,micssr.service 内容如下:

```apache
[Unit]
Description=ssr for mic 
After=network.target

[Service]
StartLimitIntervalSec=0
StartLimitBurst=5
User=dzmfg
WorkingDirectory=/home/dzmfg/tools/ssr/
ExecStart=/home/dzmfg/tools/ssr/ssserver -c ssr.json
Restart=always
RestartSec=1

[Install]
WantedBy=multi-user.target

```

通过自己的机器,将ssh密钥传上去.

```bash
#自己的机器
ssh-copy-id dzmfg@xxx.xxx.xxx.xxx
```

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
