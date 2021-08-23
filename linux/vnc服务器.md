---
title: VNC 服务器
description: VNC 服务器
published: true
date: 2021-08-23T04:01:37.940Z
tags: vnc 服务器
editor: markdown
dateCreated: 2021-08-22T08:40:42.123Z
---

# 1. VNC安装配置
```sh
dnf install tigervnc-server tigervnc-server-module -y
yum -y install vnc-server
```

## 1.1. 拷贝配置文件
```sh
[root@xwq ~]# cd /lib/systemd/system
[root@xwq system]# cp  vncserver@.service   vncserver@:1.service
```
> 注：VNC 服务本身使用的是5900端口。鉴于有不同的用户使用 VNC ，每个人的连接都会获得不同的端口。配置文件名里面的数字告诉 VNC 服务器把服务运行在5900的子端口上。在我们这个例子里，第一个 VNC 服务会运行在5901（5900 + 1）端口上，之后的依次增加，运行在5900 + x 号端口上。其中 x 是指之后用户的配置文件名 vncserver@:x.service 里面的 x 。
> 如果要用更多的用户连接，需要创建配置文件和端口，添加一个新的用户和端口。你需要创建 vncserver@:2.service 并替换配置文件里的用户名和之后步骤里相应的文件名、端口号。请确保你登录 VNC 服务器用的是你之前配置 VNC 密码的时候使用的那个用户名。

```sh
vim vncserver@:1.service
```
## 1.2. 调整分辨率和加入root 用户
* 修改前：

```sh
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target
[Service]
Type=forking
2. Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
PIDFile=/home/<USER>/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
[Install]
WantedBy=multi-user.target
```

* 修改后：

```sh
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target
[Service]
Type=forking
2. Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver %i -geometry 1376x730"
PIDFile=/root/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
[Install]
WantedBy=multi-user.target
```
* 重新加载守护进程
```sh
systemctl daemon-reload
```
* 设置密码：
```sh
vncpasswd root
```
* 启动vncserver
```sh
[root@xwq system]# vncserver :1
```
* 开启守护进程
```sh
[root@xwq system]# systemctl enable vncserver@:1.service
```
* 添加防火墙规则
```sh
[root@xwq ~]# firewall-cmd --permanent --zone=public --add-port=5901/tcp
[root@xwq ~]# firewall-cmd --reload
```
* 关闭vncserver 
* 关闭vnc服务：
```sh
systemctl stop vncserver@:1.service
```
*  禁止 VNC 服务开机启动：
```sh
systemctl disable vncserver@:1.service
```
*  关闭防火墙：
```sh
systemctl stop firewalld.service
```
