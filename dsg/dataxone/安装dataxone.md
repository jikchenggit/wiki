---
title: 安装dataxone
description: 安装dataxone
published: true
date: 2021-08-23T03:24:34.117Z
tags: 安装, dataxone
editor: markdown
dateCreated: 2020-03-17T13:16:22.382Z
---

# 1. 安装手册

           

|    **软件包**     |            **软件版本**             |                                **存放目录**                                |
| ----------------- | ----------------------------------- | -------------------------------------------------------------------------- |
| MySQL软件包        | mysql-community-5.7.19-1.el7.x86_64 | [MySQL](https://dev.mysql.com/downloads/mysql/)                            |
| JDK软件包          | jdk-8u241-linux-x64.tar.gz          | [jdk](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html) |
| Tomcat软件包       | apache-tomcat-8.5.32.tar.gz         | [tomcat](https://tomcat.apache.org/download-10.cgi)                        |
| DataXone平台软件包 |                                     | dataXone/dataXone_ver_*time*.tar.gz                                        |
| DataXone平台模块包 |                                     | dataXone/SODT_D_Mc...dataXone/SODT_D_M/java...                             |

## 1.1. 获取相关软件包链接

[MySQL](https://dev.mysql.com/downloads/mysql/)
[tomcat](https://tomcat.apache.org/download-10.cgi)
[jdk](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html) 
[DataXone平台软件包](192.168.1.186) 请联系DSG人员。
[DataXone平台模块包](192.168.1.186) 请联系DSG人员。

# 2. 创建用户
```bash
useradd dsg
passwd dsg
su - dsg
```

# 3. 创建安装目录
```bash
mkdir -p  dataxone mysql engineDsg/c/SODT_D_M/Supersync engineDsg/java/kafka
```

# 4. 安装MySQL

## 4.1. 安装


[用RPM包离线安装mysql到linux](http://aming.ddns.net:8900/zh/mysql/安装和升级/使用RPM包离线安装mysql到linux)

## 4.2. 安装完后测试


[安装后的设置和测试](http://aming.ddns.net:8900/zh/mysql/安装和升级/安装后的设置和测试)
## 4.3. 针对mysql 特殊配置

### 4.3.1. 修改字符集和链接数
```
[mysqld]
max_connections=10000
max_user_connections=1000
lower_case_table_names=1
character-set-server=utf8
[client]
default-character-set=utf8
```
### 4.3.2. 重启MySQL
```bash
 systemctl restart mysqld
```
## 4.4. 创建数据库用户dsg
```bash
create user dsg@'%' identified by '1234';
grant all privileges on *.* to dsg@'%' ;
```

# 5. 安装dataxone
## 5.1. 解压dataxone
```bash
tar -zxf dataXone_ver*.tar.gz -C /home/dsg/dataxone
```
## 5.2. 把组件包放入目录中
```bash
mv  apache-tomcat-*.tar.gz  jdk-*-linux-x64.tar.gz  dataXone_ver_*/
```

## 5.3. 进入目录运行安装脚本
```bash
cd /home/dsg/dataxone/dataxone_ver*
./dataxone_setup.sh
```
## 5.4. 脚本安装记录

```bash
[liutt@host65 dataXone_ver2019111315]$ ./dataxone_setup.sh 
        ##############################################################
        #                                                            #
        #      #######      ######     #######                       #
        #      #      #    #      #   #       #        DSG SoftWare  #
        #      #      #    #      #   #                              #
        #      #      #    #    #     #                              #
        #      #      #     ######    #  #####                       #
        #      #      #           #   #       #                      #
        #      #      #    #      #   #       #                      #
        #      #      #    #      #   #       #                      #
        #      #######      ######     #######                       #
        #                                                            #
        #                                                            #
        ##############################################################
        ==============================================================
                     Welcome to install DSG SoftWare                  
        ==============================================================
==============================================================================
                       Prepare to install dataXone                            
==============================================================================
 
Please specify the storage directory of the dataXone package[/usr/liutt/liutt/dataXone_ver2019111315(default)]:

>>List and check dataXone packages:
 -  - 
liutt - liutt - automated_screen_theme_report_db_2019111315.sql
liutt - liutt - autoMaticEngineBoot-1.0.0.war_2019111315
liutt - liutt - automation_dsg_db_2019111315.sql.tar.gz
liutt - liutt - dataxone_bigdatas_db_2019111315.sql
liutt - liutt - dataxone_c_info_2019111315.sql
liutt - liutt - dataxone_pmon.sql
liutt - liutt - dataXone_theme_dsg_project-0.0.1.war_2019111315
liutt - liutt - dataXone_unstructuredData_project_nogit-0.0.2.war_2019111315
liutt - liutt - DSGWEB.war_2019111315
liutt - liutt - permission_dsg_2019111315.sql
liutt - liutt - permission.war_2019111315
liutt - liutt - yxad.mysql.tar.gz

INFO : All required packages exist!
Please specify the current host IP to install dataXone: 10.0.0.65
Please specify the tomcat port of the dataxone main platform[8081(default)]:
Please specify the tomcat port of the dataxone KPI[8080(default)]:
Please specify mysql Ip of dataXone[10.0.0.65(default)]:
Please specify mysql port of dataXone[3306(default)]:
Please specify mysql connection user: dsg
Please specify mysql connection password: dsg
Please specify the dataXone installation directory[/home/dsg(default)]: /usr/liutt/ins


```
# 6. 启动服务
- 生效环境变量
```bash
su - dsg
$ source ~/.bash_profile
```
## 6.1. 启动tomcat for kpi theme
```bash
cd  /home/dsg/tomcat/apache-tomcat-8.5.32/bin
$ sh startup.sh
```
## 6.2. 启动tomcat for dataxone main platform。

```bash
$ cd  /home/dsg/dataXoneTomcat/apache-tomcat-8.5.32/bin
$ sh startup.sh
```

## 6.3. 查看日志
```bash
$ cd /home/dsg/dataXoneTomcat/apache-tomcat-8.5.32/logs
$ tail -f catalina.out
```
## 6.4. 登录dataxone
浏览器访问[http://IP:8080/DSGWEB/#/login](http://IP:8080/DSGWEB/#/login)
默认登录`用户名`/`密码`：`dataxone`/`123456`

