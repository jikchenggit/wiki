---
title: 安装PXF
description: 安装PXF
published: true
date: 2021-08-23T03:59:41.612Z
tags: 安装pxf
editor: markdown
dateCreated: 2021-01-29T06:29:18.762Z
---

# 前提条件
建议在所有Greenplum数据库主机上安装PXF。安装PXF前，请确保满足以下前提条件:

1. greenplum 5.21.2 及以上版本.
2. 可以访问greenplum数据库集群中所有的主机.(master  主机,slave 主机,segment 主机).
3. 必须拥有操作系统超级用户,或者具有sudo 权限,才能安装PXF 包.如果在`CentOS/RHEL上安装,可以选择安装自定义目录.
4. 在所有greenplum 数据库主机上安装JAVA 8 或 JAVA 11 ,请参见[JAVA 安装](/zh/greenplum/PXF/install_PXF/install_java).
5. 您可以识别拥有PXF安装的操作系统用户。该用户必须与拥有Greenplum数据库安装的用户相同，或者与拥有Greenplum数据库安装目录写权限的用户相同。
6. 如果您之前配置过，并且在您的Greenplum安装中使用PXF,请升级或者卸载.
# 下载PXF 软件包
1. 进入[VMware Tanzu Network](https://network.pivotal.io/products/pivotal-gpdb/)，找到并选择名为`Greenplum Platform Extension Framework`的发布下载目录。
PXF下载文件名的格式为 
`pxf-gp<greenplum-major-version>-<pxf-version>-<pkg-version>.<platform>.<file_type>. `
例如:
  `pxf-gp6-5.16.0-2.el7.x86_64.rpm`
  或者
  `pxf-gp6-5.16.0-2-ubuntu18.04-amd64.deb`
  
2. 选择对应greenplum 数据库主版本和操作系统平台对应的PXF 包.
3. 进行下载.


# 安装PXF 包.
  必须在Greenplum Database master和standby master主机以及每个段主机上安装PXF包。
  
  如果您在主机上安装了一个较旧版本的PXF包，那么安装一个较新的包将删除现有的PXF安装，并安装新版本。
  
  安装步骤如下
  
1. 找到从VMware Tanzu Network下载的安装程序文件。
2. 创建一个文本文件，列出您的Greenplum数据库备用主主机和段主机，每行一个主机名。例如，名为gphostfile的文件可能包括:
```bash
gpmaster
mstandby
seghost1
seghost2
seghost3
```
3. 将下载的PXF 文件包复制到每台greenplum 集群中的所有主机. 
```
gphost$ gpscp -f gphostfile pxf-gp6-5.16.0-2.el7.x86_64.rpm =:/tmp/
```
4. 使用`rpm` 命令在每个greenplum 数据库主机上安装.如果存在以前的PXF安装,请卸载. 
a. 将PXF 安装到所有greenplum 主机的默认位置.

在CentOS/RHEL系统上: 
```
gphost$ gpssh -e -v -f gphostfile "sudo rpm -Uvh /tmp/pxf-gp6-5.16.0-2.el7.x86_64.rpm"

```
在Ubuntu系统上:
```
gphost$ gpssh -e -v -f gphostfile "sudo dpkg --install /tmp/pxf-gp6-5.16.0-2-ubuntu18.04-amd64.deb"

```
PXF包默认安装目录为`/usr/local/pxf-gp<greenplum-major-version>`。


b.在所有的Greenplum主机上安装PXF到自定义目录(仅适用于CentOS/RHEL):
 ```bash
 gpadmin@gphost$ gpssh -e -v -f gphostfile "sudo rpm -Uvh --prefix <install-location> pxf-gp6-5.16.0-2.el7.x86_64.rpm"
 ```
5. 设置PXF安装文件的所有权和权限，以允许gpadmin用户访问。例如，如果你把PXF安装到默认位置:
```bash

gphost$ gpssh -e -v -f gphostfile "sudo chown -R gpadmin:gpadmin /usr/local/pxf-gp*"
```
如果您在CentOS/RHEL上将PXF安装到自定义的`<install-location>`，请在命令中指定该位置。
6. (可选)将“PXF bin”目录添加到PXF所有者的“$”路径下。例如，如果您在默认位置安装了针对Greenplum 6的PXF，您可以向gpadmin用户的.bashrc shell初始化脚本添加以下文本:

```bash
export PATH=$PATH:/usr/local/pxf-gp6/bin
```
请确保删除`$GPHOME/PXF/bin`中先前添加的PXF的$PATH条目。
7. 删除复制到每个系统中的PXF包下载文件。例如，从/tmp中删除rpm:

```bash
gpadmin@gphost$ gpssh -e -v -f gphostfile "rm -f /tmp/pxf-gp6-5.16.0-2.el7.x86_64.rpm"

```
# 下一步
PXF在安装后没有激活。在使用PXF之前，必须显式初始化并启动PXF服务器。

- 查看[关于PXF安装和配置目录](/greenplum/PXF/manager_PXF/install_directory)的重要PXF文件和目录的列表和描述。

- 如果这是您第一次使用PXF，请查看[配置PXF](/greenplum/PXF/manager_PXF/config_pxf)，了解在使用PXF之前必须执行的初始化和配置过程的描述。

- 如果您将PXF rpm或deb作为Greenplum数据库升级安装，请返回到那些升级说明。

- 如果您将PXF rpm或deb安装到您已经配置并正在使用PXF的Greenplum集群中，则可能需要执行一些升级操作。召回PXF的原始版本(安装rpm或deb之前)，并执行PXF升级步骤的步骤2。