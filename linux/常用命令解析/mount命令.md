---
title: mount命令
description: mount命令
published: true
date: 2021-08-23T03:26:13.115Z
tags: 命令, mount
editor: markdown
dateCreated: 2020-03-20T03:53:18.983Z
---

# mount 命令
Linux上用来挂载媒体的命令叫作mount。默认情况下，mount命令会输出当前系统上挂载的设备列表。

```bash
$ mount
/dev/mapper/VolGroup00-LogVol00 on / type ext3 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
/dev/sda1 on /boot type ext3 (rw)
tmpfs on /dev/shm type tmpfs (rw)
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw)
/dev/sdb1 on /media/disk type vfat
(rw,nosuid,nodev,uhelper=hal,shortname=lower,uid=503)
$
```

mount命令提供如下四部分信息：

  - 媒体的设备文件名
  - 媒体挂载到虚拟目录的挂载点
  - 文件系统类型
	- 已挂载媒体的访问状态
  
上面例子的最后一行输出中，U盘被GNOME桌面自动挂载到了挂载点/media/disk。vfat文件系统类型说明它是在Windows机器上被格式化的。

要手动在虚拟目录中挂载设备，需要以root用户身份登录，或是以root用户身份运行sudo命令。下面是手动挂载媒体设备的基本命令：

```
mount -t type device directory
```

type参数指定了磁盘被格式化的文件系统类型。Linux可以识别非常多的文件系统类型。如果是和Windows PC共用这些存储设备，通常得使用下列文件系统类型。

  - vfat：Windows长文件系统。
  - ntfs：Windows NT、XP、Vista以及Windows 7中广泛使用的高级文件系统。
  - iso9660：标准CD-ROM文件系统
  
  
**`mount`命令的参数**

|    参数     |                         描述                         |
| ----------- | ---------------------------------------------------- |
| `-a`        | 挂载/etc/fstab文件中指定的所有文件系统                 |
| `-f`        | 使`mount`命令模拟挂载设备，但并不真的挂载               |
| `-F`        | 和`-a`参数一起使用时，会同时挂载所有文件系统            |
| `-v`        | 详细模式，将会说明挂载设备的每一步                      |
| `-I`        | 不启用任何/sbin/mount.filesystem下的文件系统帮助文件   |
| `-l`        | 给ext2、ext3或XFS文件系统自动添加文件系统标签           |
| `-n`        | 挂载设备，但不注册到/etc/mtab已挂载设备文件中           |
| `-p *num*`  | 进行加密挂载时，从文件描述符*`num`*中获得密码短语       |
| `-s`        | 忽略该文件系统不支持的挂载选项                         |
| `-r`        | 将设备挂载为只读的                                    |
| `-w`        | 将设备挂载为可读写的（默认参数）                       |
| `-L label`  | 将设备按指定的*`label`*挂载                           |
| `-U *uuid*` | 将设备按指定的*`uuid`*挂载                            |
| `-O`        | 和`-a`参数一起使用，限制命令只作用到特定的一组文件系统上 |
| `-o`        | 给文件系统添加特定的选项                               |

-o参数允许在挂载文件系统时添加一些以逗号分隔的额外选项。以下为常用的选项。

  - ro：以只读形式挂载。
  - rw：以读写形式挂载。
  - user：允许普通用户挂载文件系统。
  - check=none：挂载文件系统时不进行完整性校验。
  - loop：挂载一个文件。

# 例子:
- 1. 挂载光盘镜像:
```
mount -o loop -t iso9660 /app/OEL_7.5.iso /mnt
```
- 2. 重新挂载
```bash
mount -o remount /dev/shm
```
- 3. 磁盘挂载
```sh
mount /dev/sdb1 /u01
```
- 4. 挂载lvm 
```bash
mount /dev/volume-group1/dsg /dsg
```