---
title: oracle19crac最佳实践
description: oracle19crac最佳实践
published: true
date: 2021-08-23T03:26:28.287Z
tags: oracle19crac最佳实践
editor: markdown
dateCreated: 2020-03-20T07:57:09.737Z
---

# 1. 安装需求条件：
|   条目   |                         要求                         |
| ------- | --------------------------------------------------- |
| 操作系统 | Oracle Linux 7.7,redhat 7.7。                        |
| yum 源   | 本地yum源，需要操作系统镜像                           |
| 磁盘     | /u01至少200G。                                       |
| 内存     | RAM至少16G，swap 空间为 16G *2 =32G                  |
| 多路径   | 使用mutipatch,udev, 或Oracleasmlib                   |
| IP 地址  | 5 个公网IP,两个私有IP。                               |
| 主机名   | 2 个主机名,2 个VIP 名,2个私有网卡名,一个scan-ip 名称。 |

# 2. 安装步骤
## 2.1. 创建yum 源
### 2.1.1. 查看是否配置了yum 源
```bash
cat /et/yum.repos.d/public-yum-ol7.repo 
```
### 2.1.2. 创建yum 源
```bash
cat >>  /etc/yum.repos.d/public-yum-ol7.repo  << EOF
[redhat7.7]
name=Oracle Linux Server 7.7
baseurl=file:///mnt/cdrom/Server/
gpgkey=file:///media/RPM-GPG-KEY-oracle
gpgcheck=0
enabled=1
EOF
```
### 2.1.3. 启用32 包  
```bash
echo 'multilib_policy=all' >> /etc/yum.conf
```
### 2.1.4. 清除yum缓存
```bash
yum clean all
yum makecache
```
## 2.2. 安装相关包

### 2.2.1. 检测软件依赖包

```bash
rpm -q --qf '%{NAME}-%{VERSION}-%{RELEASE} (%{ARCH})\n' binutils \
compat-libcap1 \
compat-libstdc++- \
gcc \
gcc-c++ \
libaio \
libaio-devel \
glibc \
glibc-devel \
ksh \
make \
OpenSSH \
libgcc \
libstdc++ \
libstdc++-devel \
libXtst \
libX11 \
libXau \
libxcb \
libXi \
net-tools \
sysstat \
smartmontools
```

### 2.2.2. 安装软件包
```bash
yum install -y binutils
yum install -y compat-libcap1
yum install -y compat-libstdc++*
yum install -y glibc*
yum install -y glibc-devel*
yum install -y ksh
yum install -y OpenSSH
yum install -y libgcc*
yum install -y libstdc++*
yum install -y libstdc++-devel*
yum install -y libaio*
yum install -y libaio-devel*
yum install -y libXtst*
yum install -y libX11*
yum install -y libXau*
yum install -y libxcb*
yum install -y libXi*
yum install -y make
yum install -y net-tools
yum install -y sysstat
yum install -y smartmontools

yum install bind-libs bind-utils compat-libcap1 compat-libstdc++-33 gssproxy keyutils ksh \
libICE libSM libX11 libX11-common libXau libXext libXi libXinerama libXmu \
libXrandr libXrender libXt libXtst libXv libXxf86dga libXxf86misc libXxf86vm \
libaio-devel libbasicobjects libcollection libdmx libevent libini_config \
libnfsidmap libpath_utils libref_array libstdc++-devel libtirpc libverto-libevent \
libxcb lm_sensors-libs mailx nfs-utils psmisc quota quota-nls rpcbind \
smartmontools sysstat tcp_wrappers unzip xorg-x11-utils xorg-x11-xauth

```

# 3. 操作系统配置
## 3.1. 关闭selinux
```bash
cat > /etc/selinux/config << EOF
# 4. This file controls the state of SELinux on the system.
# 5. SELINUX= can take one of these three values:
# 6. enforcing - SELinux security policy is enforced.
# 7. permissive - SELinux prints warnings instead of enforcing.
# 8. disabled - No SELinux policy is loaded.
SELINUX=disabled
# 9. SELINUXTYPE= can take one of three two values:
# 10. targeted - Targeted processes are protected,
# 11. minimum - Modification of targeted policy. Only selected processes are protected.
# 12. mls - Multi Level Security protection.
SELINUXTYPE=targeted
EOF	
```
## 3.2. 关闭防火墙
```bash
systemctl status firewalld.service
systemctl stop firewalld.service
systemctl disable firewalld.service

systemctl status NetworkManager
systemctl stop NetworkManager
systemctl disable NetworkManager

systemctl status avahi-daemon
systemctl stop avahi-daemon
systemctl disable avahi-daemon

```
## 3.3. 关闭透明大页
```bash
cat >> /etc/default/grub << EOF
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=myvg/swap rd.lvm.lv=myvg/usr
vconsole.font=latarcyrheb-sun16 rd.lvm.lv=myvg/root crashkernel=auto
vconsole.keymap=us rhgb quiet transparent_hugepage=never"
GRUB_DISABLE_RECOVERY="true"
EOF
```
## 3.4. 设置系统限制

```bash
cat >> /etc/sysctl.conf << EOF
######################
oracle soft core unlimited
oracle hard core unlimited
oracle soft nproc 16384
oracle hard nproc 16384
oracle soft nofile 65536
oracle hard nofile 65536
oracle soft memlock 363331584
oracle hard memlock 363331584
oracle  soft  stack  10240
oracle  hard  stack  32768
######################
grid soft core unlimited
grid hard core unlimited
grid soft nproc 16384
grid hard nproc 16384
grid soft nofile 65536
grid hard nofile 65536
grid soft memlock 363331584
grid hard memlock 363331584
grid  soft  stack  10240
grid  hard  stack  32768
EOF
```
## 3.5. 设置登录认证：
```bash
cat >> /etc/pam.d/login << EOF
session    required     /lib64/security/pam_limits.so
EOF
```
## 3.6. 设置内核参数
```bash
cat >> /etc/sysctl.conf << EOF
# 8. for oracle
vm.swappiness = 1  
vm.dirty_background_ratio = 3
vm.dirty_ratio = 80
vm.dirty_expire_centisecs = 500
vm.dirty_writeback_centisecs = 100
kernel.shmall = 4294967296
kernel.shmmni = 4096
kernel.shmmax = 4398046511104
kernel.sem = 250 32000 100 128
kernel.panic_on_oops = 1
fs.aio-max-nr = 1048576
fs.file-max = 6815744
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
net.ipv4.conf.all.rp_filter = 2
net.ipv4.conf.default.rp_filter = 2
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 8192
net.core.somaxconn = 10240
EOF
```
## 3.7. 生效：
```bash
sysctl -p
```
## 3.8. 设置登录参数
```bash
cat >> /etc/profile.d/oracle-grid.sh << EOF
#Setting the appropriate ulimits for oracle and grid user
if [ $USER = "oracle" ]; then
if [ $SHELL = "/bin/ksh" ]; then
ulimit -u 16384 
ulimit -n 65536
else
ulimit -u 16384 -n 65536
fi
fi
if [ $USER = "grid" ]; then
if [ $SHELL = "/bin/ksh" ]; then
ulimit -u 16384
ulimit -n 65536
else
ulimit -u 16384 -n 65536
fi
fi
EOF
```

## 3.9. 创建用户
```bash
groupadd -g 54321 oinstall
groupadd -g 54322 dba
groupadd -g 54323 oper
groupadd -g 54324 asmadmin
groupadd -g 54325 asmdba
groupadd -g 54326 asmoper
groupadd -g 54327 backupdba
groupadd -g 54328 dgdba
groupadd -g 54329 racdba
groupadd -g 54330 kmdba
useradd -u 54321 -g oinstall -G dba,oper,asmadmin,asmdba,backupdba,dgdba,racdba,kmdba -d /home/oracle oracle
useradd -u 54322 -g oinstall -G dba,asmadmin,asmdba,asmoper -d /home/grid grid	
```
## 3.10. 修改密码
```bash
passwd oracle 
passwd grid
```
## 3.11. 用户环境变量
### 3.11.1. 创建grid用户环境变量
```bash
cat >> ~/.bash_profile << EOF
if [ $USER = "oracle" ] || [ $USER = "grid" ]; then
        if [ $SHELL = "/bin/ksh" ]; then
         ulimit -p 16384
              ulimit -n 65536
        else
              ulimit -u 16384 -n 65536
        fi
        umask 022
fi

export ORACLE_BASE=/u01/app/grid
export ORACLE_HOME=/u01/app/19.3.0/grid
export ORACLE_SID=+ASM1
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$ORACLE_HOME/rdbms/lib:/lib:/usr/lib
export SHLIB_PATH=$ORACLE_HOME/lib32:$ORACLE_HOME/rdbms/lib32
export PATH=$ORACLE_HOME/bin:/usr/local/bin:/usr/bin:/usr/sbin:/usr/bin/X11:$PATH
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib:$ORACLE_HOME/network/jlib
umask 022
set -o vi

NAME=`hostname`
PS1="[$NAME:$LOGNAME]:\${PWD}>"  

echo ORACLE_SID=$ORACLE_SID
echo ORACLE_BASE=$ORACLE_BASE
echo ORACLE_HOME=$ORACLE_HOME
EOF
```

### 3.11.2. Oracle 用户变量
```bash
cat >> ~/.bash_profile << EOF
if [ $USER = "oracle" ] || [ $USER = "grid" ]; then
        if [ $SHELL = "/bin/ksh" ]; then
         ulimit -p 16384
              ulimit -n 65536
        else
              ulimit -u 16384 -n 65536
        fi
        umask 022
fi

export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
export ORACLE_SID=orcl1
export ORACLE_UNQNAME=orcl
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$ORACLE_HOME/rdbms/lib:/lib:/usr/lib
export SHLIB_PATH=$ORACLE_HOME/lib32:$ORACLE_HOME/rdbms/lib32
export PATH=$ORACLE_HOME/bin:/usr/local/bin:/usr/bin:/usr/sbin:/usr/bin/X11:$PATH
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib:$ORACLE_HOME/network/jlib
umask 022
set -o vi

NAME=`hostname`
PS1="[$NAME:$LOGNAME]:\${PWD}>"  

echo ORACLE_SID=$ORACLE_SID
echo ORACLE_BASE=$ORACLE_BASE
echo ORACLE_HOME=$ORACLE_HOME
EOF

```
## 3.12. hosts 配置
```bash
cat >> /etc/hosts << EOF
127.0.0.1   localhost        localhost .localdomain
#Public
10.4.96.1    xy-sxfdb1.sx.com         xy-sxfdb1        
10.4.96.2    xy-sxfdb2.sx.com         xy-sxfdb2        
#Private                                               
192.168.1.1  xy-sxfdb1-priv1
192.168.1.2  xy-sxfdb1-priv2
192.168.2.1  xy-sxfdb2-priv1
192.168.2.2  xy-sxfdb2-priv2
# 14. Virtual                                              
10.4.96.3    xy-sxfdb1-vip.sx.com     xy-sxfdb1-vip    
10.4.96.4    xy-sxfdb2-vip.sx.com     xy-sxfdb2-vip
# sanip
10.4.96.4    xy-sxfdb.sx.com
EOF
```
## 3.13. 创建目录
```bash
mkdir -p /u01/app/agent		
mkdir -p /u01/app/oraInventory		
mkdir -p /u01/app/grid		
mkdir -p /u01/app/19.3.0/grid			
mkdir -p /u01/app/oracle/product/19.3.0/dbhome_1		
chown -R grid:oinstall /u01/app/grid
chown -R grid:oinstall /u01/app/19.3.0	
chown -R grid:oinstall /u01/app/oraInventory
chmod -R 755 /u01/app/oraInventory
chown -R oracle:oinstall /u01/app/oracle	
chown -R oracle:oinstall /u01/app/agent	
chmod -R 755 /u01

```
### 3.13.1. 设置psu补丁目录
```bash
mkdir -p /u01/setup/patch
mkdir -p /u01/setup/db
mkdir -p /u01/setup/grid
chown -R grid:oinstall /u01/setup/grid
chown -R oracle:oinstall /u01/setup/db
chown -R grid:oinstall /u01/setup/patch
```

## 3.14. 时区
```bash
echo 'NOZEROCONF=yes' >> /etc/sysconfig/network
```
## 3.15. 配置DNS
```bash
cat >> /etc/resolv.conf << EOF
nameserver 192.168.27.30
EOF
```
## 3.16. 配置网卡增加DNS 信息
```bash
DEVICE=eth0
TYPE=Ethernet
UUID=d380d8b1-3c38-4f47-8398-453cd486e970
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=none
HWADDR=00:0C:29:29:E1:4F
IPADDR=192.168.27.50
PREFIX=24
GATEWAY=192.168.27.1
DNS1=192.168.27.30
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
```



## 3.17. 配置NTP 服务
- /etc/ntp.conf
```bash
server 192.168.27.30 iburs
```

设置NTP服务的目的，是让构建RAC环境的两台机器的时间保持一致
让其中一台为主，另外的NTP服务指到该服务器即可。
在11GR2，新增加了一个CTSS进程，专门用于时间同步，因此，需要以下配置开启NTPD服务，但必须以-X选项启动NTP服务
### 3.17.1. 安装ntp
```bash
yum install ntp
```
### 3.17.2. 配置文件
```bash


rac1:~ # cat /etc/ntp.conf 
################################################################################
## 19.1. /etc/ntp.conf
##
## 19.2. Sample NTP configuration file.
## 19.3. See package 'ntp-doc' for documentation, Mini-HOWTO and FAQ.
## 19.4. Copyright (c) 1998 S.u.S.E. GmbH Fuerth, Germany.
##
## 19.5. Author: Michael Andres,  <ma@suse.de>
## 19.6. Michael Skibbe,  <mskibbe@suse.de>
##
################################################################################

##
## 19.7. Radio and modem clocks by convention have addresses in the 
## 19.8. form 127.127.t.u, where t is the clock type and u is a unit 
## 19.9. number in the range 0-3. 
##
## 19.10. Most of these clocks require support in the form of a 
## 19.11. serial port or special bus peripheral. The particular  
## 19.12. device is normally specified by adding a soft link 
## 19.13. /dev/device-u to the particular hardware device involved, 
## 19.14. where u correspond to the unit number above. 
## 
## 19.15. Generic DCF77 clock on serial port (Conrad DCF77)
## 19.16. Address:     127.127.8.u
## 19.17. Serial Port: /dev/refclock-u
##  
## 19.18. (create soft link /dev/refclock-0 to the particular ttyS?)
##
# 20. server 127.127.8.0 mode 5 prefer

##
## 20.1. Undisciplined Local Clock. This is a fake driver intended for backup
## 20.2. and when no outside source of synchronized time is available.
##
server 127.127.1.0              # local clock (LCL)
fudge  127.127.1.0 stratum 10   # LCL is unsynchronized

##
## 20.3. Add external Servers using
## 20.4. # rcntp addserver <yourserver>
## 

# 21. Access control configuration; see /usr/share/doc/packages/ntp/html/accopt.html for
# 22. details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# 23. might also be helpful.
#
# 24. Note that "restrict" applies to both servers and clients, so a configuration
# 25. that might be intended to block requests from certain clients could also end
# 26. up blocking replies from your own upstream servers.

# 27. By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery
restrict -6 default kod notrap nomodify nopeer noquery

# 28. Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# 29. Allow ntpdc to stay backwards compatible with SLE-11 before SP4.
enable mode7

# 30. Clients from this (example!) subnet have unlimited access, but only if
# 31. cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust

##
## 31.1. Miscellaneous stuff
##

driftfile /var/lib/ntp/drift/ntp.drift # path for drift file

logfile   /var/log/ntp          # alternate log file
# 32. logconfig =syncstatus + sysevents
# 33. logconfig =all

# 34. statsdir /tmp/                # directory for statistics files
# 35. filegen peerstats  file peerstats  type day enable
# 36. filegen loopstats  file loopstats  type day enable
# 37. filegen clockstats file clockstats type day enable

#
# 38. Authentication stuff
#
keys /etc/ntp.keys              # path for keys file
trustedkey 1                    # define trusted keys
requestkey 1                    # key (7) for accessing server variables
# 39. controlkey 15                 # key (6) for accessing server variables

################################################################################
## 39.1. /etc/ntp.conf
##
## 39.2. Sample NTP configuration file.
## 39.3. See package 'ntp-doc' for documentation, Mini-HOWTO and FAQ.
## 39.4. Copyright (c) 1998 S.u.S.E. GmbH Fuerth, Germany.
##
## 39.5. Author: Michael Andres,  <ma@suse.de>
## 39.6. Michael Skibbe,  <mskibbe@suse.de>
##
################################################################################

##
## 39.7. Radio and modem clocks by convention have addresses in the 
## 39.8. form 127.127.t.u, where t is the clock type and u is a unit 
## 39.9. number in the range 0-3. 
##
## 39.10. Most of these clocks require support in the form of a 
## 39.11. serial port or special bus peripheral. The particular  
## 39.12. device is normally specified by adding a soft link 
## 39.13. /dev/device-u to the particular hardware device involved, 
## 39.14. where u correspond to the unit number above. 
## 
## 39.15. Generic DCF77 clock on serial port (Conrad DCF77)
## 39.16. Address:     127.127.8.u
## 39.17. Serial Port: /dev/refclock-u
##  
## 39.18. (create soft link /dev/refclock-0 to the particular ttyS?)
##
# 40. server 127.127.8.0 mode 5 prefer

##
## 40.1. Undisciplined Local Clock. This is a fake driver intended for backup
## 40.2. and when no outside source of synchronized time is available.
##
server rac1             # local clock (LCL)
fudge  127.127.1.0 stratum 10   # LCL is unsynchronized

##
## 40.3. Add external Servers using
## 40.4. # rcntp addserver <yourserver>
## 

# 41. Access control configuration; see /usr/share/doc/packages/ntp/html/accopt.html for
# 42. details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# 43. might also be helpful.
#
# 44. Note that "restrict" applies to both servers and clients, so a configuration
# 45. that might be intended to block requests from certain clients could also end
# 46. up blocking replies from your own upstream servers.

# 47. By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery
restrict -6 default kod notrap nomodify nopeer noquery

# 48. Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# 49. Allow ntpdc to stay backwards compatible with SLE-11 before SP4.
enable mode7

# 50. Clients from this (example!) subnet have unlimited access, but only if
# 51. cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust

##
## 51.1. Miscellaneous stuff
##

driftfile /var/lib/ntp/drift/ntp.drift # path for drift file

logfile   /var/log/ntp          # alternate log file
# 52. logconfig =syncstatus + sysevents
# 53. logconfig =all

# 54. statsdir /tmp/                # directory for statistics files
# 55. filegen peerstats  file peerstats  type day enable
# 56. filegen loopstats  file loopstats  type day enable
# 57. filegen clockstats file clockstats type day enable

#
# 58. Authentication stuff
#
keys /etc/ntp.keys              # path for keys file
trustedkey 1                    # define trusted keys
requestkey 1                    # key (7) for accessing server variables
# 59. controlkey 15                 # key (6) for accessing server variables
```



### 3.17.3. 配置客户端RAC2 服务器
```bash
vi /etc/ntp/step-tickers
rac1
```


### 3.17.4. 修改文件/etc/sysconfig/ntpd，增加-x 选项
```bash
  vi /etc/sysconfig/ntpd
  OPTIONS="-x -u ntp:ntp -p /var/run/ntpd.pid"
```
### 3.17.5. RAC1 开启时间同步服务器
```bash
systemctl  start ntpd
```
### 3.17.6. 开机自启动
```bash
systemctl  enable ntpd
```
- 注意参数配置如下

```bash
#restrict default nomodify notrap nopeer noquery
restrict default modify

……………………………….
# 20. Use public servers from the pool.ntp.org project.
# 21. Please consider joining the pool (http://www.pool.ntp.org/join.html).
server rac2
#server 0.rhel.pool.ntp.org iburst
#server 1.rhel.pool.ntp.org iburst
```
- 以上参数说明：

```bash
#restrict default nomodify notrap nopeer 
restrict default nomodify
restrict 是接受的意思
default 是默认所有主机查询
nomodify 是不能修改当前主机时间
notrap 是不允许追踪。
Noquery 是不允许查询。
```


```bash
systemctl restart ntpd.service
systemctl enable ntpd.service
systemctl status ntpd.service

```

## 3.18. 配置多路径
### 3.18.1. Udev 绑定设备
- 编辑文件：/etc/udev/rule.d/99-oracle-asmdevices.rule

```bash
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e0004652800000000", RUN+="/bin/sh -c 'mknod /dev/asm-ocr01 b  $major $minor; chown grid:asmadmin /dev/asm-ocr01; chmod 0660 /dev/asm-ocr01'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e0004654f00000001", RUN+="/bin/sh -c 'mknod /dev/asm-ocr02 b  $major $minor; chown grid:asmadmin /dev/asm-ocr02; chmod 0660 /dev/asm-ocr02'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e0004657e00000002", RUN+="/bin/sh -c 'mknod /dev/asm-ocr03 b  $major $minor; chown grid:asmadmin /dev/asm-ocr03; chmod 0660 /dev/asm-ocr03'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e0004765a00000003", RUN+="/bin/sh -c 'mknod /dev/asm-fra01 b  $major $minor; chown grid:asmadmin /dev/asm-fra01; chmod 0660 /dev/asm-fra01'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e0004767c00000004", RUN+="/bin/sh -c 'mknod /dev/asm-fra02 b  $major $minor; chown grid:asmadmin /dev/asm-fra02; chmod 0660 /dev/asm-fra02'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e000489b400000005", RUN+="/bin/sh -c 'mknod /dev/asm-data01 b  $major $minor; chown grid:asmadmin /dev/asm-data01; chmod 0660 /dev/asm-data01'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e000489dd00000006", RUN+="/bin/sh -c 'mknod /dev/asm-data02 b  $major $minor; chown grid:asmadmin /dev/asm-data02; chmod 0660 /dev/asm-data02'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e00048a0f00000007", RUN+="/bin/sh -c 'mknod /dev/asm-data03 b  $major $minor; chown grid:asmadmin /dev/asm-data03; chmod 0660 /dev/asm-data03'"
KERNEL=="dm-*", SUBSYSTEM=="block", PROGRAM=="/usr/lib/udev/scsi_id -g -u -d --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="36cc64a6100bae65e00048a3e00000008", RUN+="/bin/sh -c 'mknod /dev/asm-data04 b  $major $minor; chown grid:asmadmin /dev/asm-data04; chmod 0660 /dev/asm-data04'"

```
- 运行生效。
```bash
/sbin/udevadm trigger --type=devices --action=change
```
- 安装CV
```bash
rpm -ivh cvuqdisk-1.0.10-1.rpm
```
## 3.19. 调整/dev/shm

```bash
root@centos-fuwenchao mntsda3\]# vi /etc/fstab

/dev/mapper/ol-root / xfs defaults 0 0

/dev/mapper/ol-app /app xfs defaults 0 0

UUID=69f1e819-adc8-423f-b398-7f5fcbd12b31 /boot xfs defaults 0 0

/dev/mapper/ol-home /home xfs defaults 0 0

/dev/mapper/ol-u01 /u01 xfs defaults 0 0

/dev/mapper/ol-var /var xfs defaults 0 0

/dev/mapper/ol-swap swap                  swap defaults 0 0

tmpfs /dev/shm tmpfs defaults,size=8192M 0 0
```

-  重新挂载

```bash
mount -o remount /dev/shm
```

## 3.20. 设置root 环境变量
```bash
cat >> ~/.bash_profile << EOF
export PATH=/u01/app/grid/product/19.0.0/grid/bin:\$PATH
export LANG=C
EOF
```
# 4. 安装前检查
```bash
runcluvfy.sh stage -pre crsinst -n xy-sxfdb01,xy-sxfdb02 -verbose
```
```bash
./runInstaller.sh
```
           


# 5. PSU 补丁升级
## 5.1. GI升级脚本

>说明：
Patch 30501910 - GI Release Update 19.6.0.0.200114 在本文档中Oracle 数据home 是企业版或标准版
GI版本更新位19.6.0.0.200114 包含集群 home 和数据库home ，这些更新可以滚动升级。

### 5.1.1. 上传PSU 补丁包： 解压到指定目录

### 5.1.2. 安装前检查权限
```bash
ls -ltra /u01/app/grid/oraInventory/ContentsXML/oui-patch.xml
```
### 5.1.3. grid用户兼容性检测
```bash
su - grid
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30557433
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30489227
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30489632
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30655595
```

### 5.1.4. Oracle 用户兼容性检测
```bash
su - oracle
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30557433
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30489227
```
### 5.1.5. 进行打补丁


> 使用root 用户在每个节点上执行以下命令。
{.is-warning}



```bash
# /u01/app/grid/product/19.0.0/grid/OPatch/opatchauto apply /app/setup/30463609/30501910
```
### 5.1.6. 验证补丁是否成功
```bash
su - grid
 $ORACLE_HOME/OPatch/opatch lspatches
```
### 5.1.7. 错误解决
#### 5.1.7.1. 错误1：
- 报错日志

```bash
Patch: /app/setup/30501910/30489227
Log: /u01/app/grid/product/19.0.0/grid/cfgtoollogs/opatchauto/core/opatch/opatch2020-03-11_20-46-18PM_1.log
Reason: Failed during Patching: oracle.opatch.opatchsdk.OPatchException: ApplySession failed in system modification phase... 'ApplySession::apply failed: java.io.IOException: oracle.sysman.oui.patch.PatchException: java.io.FileNotFoundException: /u01/app/grid/oraInventory/ContentsXML/oui-patch.xml (Permission denied)' 

After fixing the cause of failure Run opatchauto resume
```
- 解决办法：
```bash
chmod -R 660 /u01/app/grid/oraInventory/
```

#### 5.1.7.2. 错误2：
- 错误日志

```bash
Possible causes are:
   ORACLE_HOME/inventory/oneoffs/30489227 is corrupted. java.lang.RuntimeException: No Patch exists,Please check
```

- 解决办法：

```bash
scp  grid@rac1:/u01/app/grid/product/19.0.0/grid/inventory/oneoffs/30489227 . -R
```


> 然后继续打补丁：由于集群环境已经关闭所以需要指定home 目录。
{.is-warning}



## 5.2. DB 升级脚本
### 5.2.1. 检查权限：
```bash
ls -ltra /u01/app/oracle/product/19.0.0/db_1/inventory/ContentsXML/oui-patch.xml 
ls -ltra /u01/app/grid/oraInventory/ContentsXML/oui-patch.xml
```
### 5.2.2. 检查兼容性
```bash
su - oracle
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30557433
 $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /app/setup/30463609/30501910/30489227
```
### 5.2.3. 开始打补丁
```bash
/u01/app/oracle/product/19.0.0/db_1/OPatch/opatchauto   apply /app/setup/30463609/30501910 -oh /u01/app/oracle/product/19.0.0/db_1/
```

### 5.2.4. 验证补丁是否成功
```bash
su - oracle
 $ORACLE_HOME/OPatch/opatch lspatches
```
### 5.2.5. 错误解决
#### 5.2.5.1. 错误1：
- 报错日志

```bash
Patch: /app/setup/30501910/30489227
Log: /u01/app/grid/product/19.0.0/grid/cfgtoollogs/opatchauto/core/opatch/opatch2020-03-11_20-46-18PM_1.log
Reason: Failed during Patching: oracle.opatch.opatchsdk.OPatchException: ApplySession failed in system modification phase... 'ApplySession::apply failed: java.io.IOException: oracle.sysman.oui.patch.PatchException: java.io.FileNotFoundException: /u01/app/grid/oraInventory/ContentsXML/oui-patch.xml (Permission denied)' 

After fixing the cause of failure Run opatchauto resume
```
- 解决办法：
```bash
chmod -R 660 /u01/app/grid/oraInventory/
```

#### 5.2.5.2. 错误2：
- 错误日志

```bash
Possible causes are:
   ORACLE_HOME/inventory/oneoffs/30489227 is corrupted. java.lang.RuntimeException: No Patch exists,Please check
```

- 解决办法：

```bash
scp  grid@rac1:/u01/app/grid/product/19.0.0/grid/inventory/oneoffs/30489227 . -R
```


> 然后继续打补丁：由于集群环境已经关闭所以需要指定home 目录。
{.is-warning}



- 错误3 ：

```bash
Patch: /app/setup/30463609/30501910/30489227
Log: /u01/app/oracle/product/19.0.0/db_1/cfgtoollogs/opatchauto/core/opatch/opatch2020-03-14_17-32-09PM_1.log
Reason: Failed during Patching: oracle.opatch.opatchsdk.OPatchException: Re-link fails on target "install_srvm". 

After fixing the cause of failure Run opatchauto resume

]
OPATCHAUTO-68061: The orchestration engine failed.
OPATCHAUTO-68061: The orchestration engine failed with return code 1
OPATCHAUTO-68061: Check the log for more details.
OPatchAuto failed.

OPatchauto session completed at Sat Mar 14 17:32:45 2020
Time taken to complete the session 1 minute, 55 seconds

 opatchauto failed with error code 42
```

> 没有安装gcc  ，请安装gcc 编译工具。

## 5.3. OJVM 升级
```bash
su -  oracle
cd /app/setup/30463609/30484981/
$ORACLE_HOME/OPatch/opatch apply 
```

