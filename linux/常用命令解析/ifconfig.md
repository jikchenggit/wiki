---
title: ifconfig
description: ifconfig
published: true
date: 2021-08-23T03:38:09.111Z
tags: ifconfig
editor: markdown
dateCreated: 2020-07-19T06:14:12.609Z
---

ifconfig 是一个用来查看、配置、启用或禁用网络接口的工具，这个工具极为常用的。可以用这个工具来临时性的配置网卡的IP地址、掩码、广播地址、网关等。也可以把 它写入一个文件中（比如/etc/rc.d/rc.local)，这样系统引导后，会读取这个文件，为网卡设置IP地址

语　　法：ifconfig [网络设备][down up -allmulti -arp -promisc][add<地址>][del<地址>][<hw<网络设备类型><硬件地址>][io_addr<I/O地址>][irq<IRQ地址>][media<网络媒介类型>][mem_start<内存地址>][metric<数目>][mtu<字节>][netmask<子网掩码>][tunnel<地址>][-broadcast<地址>][-pointopoint<地址>][IP地址]

参数：

up 启动指定网络设备/网卡
down 关闭指定网络设备/网卡
-arp 设置指定网卡是否支持ARP协议
-promisc 设置是否支持网卡的promiscuous模式，如果选择此参数，网卡将接收网络中发给它所有的数据包
-allmulti 设置是否支持多播模式，如果选择此参数，网卡将接收网络中所有的多播数据包
-a 显示全部接口信息
-s 显示摘要信息（类似于 netstat -i）
add 给指定网卡配置IPv6地址
del 删除指定网卡的IPv6地址
<硬件地址> 配置网卡最大的传输单元
mtu<字节数> 设置网卡的最大传输单元 (bytes)
netmask<子网掩码> 设置网卡的子网掩码
tunel 建立隧道
dstaddr 设定一个远端地址，建立点对点通信
-broadcast<地址> 为指定网卡设置广播协议
-pointtopoint<地址> 为网卡设置点对点通讯协议
multicast 为网卡设置组播标志
为网卡设置IPv4地址
txqueuelen<长度> 为网卡设置传输列队的长度