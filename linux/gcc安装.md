---
title: gcc安装
description: gcc安装
published: true
date: 2021-08-23T03:24:41.408Z
tags: 安装, gcc
editor: markdown
dateCreated: 2020-03-17T13:23:00.225Z
---

# 1. 安装检查
1. 使用 gcc版本要使用(版本3.4) 开始.如果使用早于3.4 版本的gcc,可能需要使用`--disable-stage1-checking`
2.  安装所有的C 标准库 和 头文件.如果不安装全将会影响所有的 `x86_64-pc-linux-gnu` 平台.请确保`32-bit libc` 库被安装,否则会报错 `fatal error: gnu/stubs-32.h: No such file’`.如果只编译64 位程序则使用`--disable-multilib` 跳过32 位包安装.
3. shell 最好是`bash shell`. 因为兼容`POSIX compatible shell`
4. awk  需要3.1.5 版本以上.
5. gzip version 1.2.4 (or later) or  bzip2 version 1.0.2 (or later)
6. GNU make 3.80 (或更高版本)
7. GNU tar version 1.14 (or later)
8. Perl version between 5.6.1 and 5.6.24
9. GNU Multiple Precision Library (GMP) version 4.3.2 (or later)
10. MPFR Library version 2.4.2 (or later)
11. MPC Library version 0.8.1 (or later)
12. isl Library version 0.15 or later.
13. zstd Library.
14. autoconf version 2.69
15. GNU m4 version 1.4.6 (or later)
16. automake version 1.15.1
17. gettext version 0.14.5 (or later)
18. gperf version 2.7.2 (or later)
19. DejaGnu 1.4.4
期望能够安装的包:
1. tcl
2. autogen version 5.5.4 (or later) and
3. guile version 1.4.1 (or later)
4. Flex version 2.5.4 (or later)
5. Texinfo version 4.7 (or later)
6. TeX (any working version)
7. Sphinx version 1.0 (or later)
8. GNU diffutils version 2.7 (or later)
9. patch version 2.5.4 (or later)

:::alert-warning
对于Oracle Linux  需要打开以下源.
[ol7_developer_EPEL]
[ol7_optional_archive]
[ol7_optional_latest]
:::

## 1.1. 一键安装依赖包:
```bash
 yum install gcc glibc*i686  glibc*  libstdc++*i686 bash awk gzip make tar perl perl-devel gmp  mpfr  mpc isl zstd autoconf m4 automake gettext gperf DejaGnu autogen guile  flex texinfo tex sphinx diffutils patch wget gcc-c++ gcc-c++-devel lbzip2 libgo bison byacc gnat
```
说明 :
以下三个库没在Oracle linux 中找到. 需要手动安装.

```
sphinx   mpc  isl 
```

:::alert-warning
sphinx  是调优用的 ,可以不必装.
:::
# 2. 下载源码:
```bash
wget http://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-9.1.0/gcc-9.1.0.tar.gz
```

使用命令下载源码:
```bash
./contrib/download_prerequisites
```
下载 mpc isl mpfr gmp 的源码.

# 3. 配置:
进入源码目录:
```bash
mkdir bld
../configuer
```
```bash
mkdir bld
../configuer --enable-languages=all
../configure  --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release  --enable-__cxa_atexit  --enable-gnu-unique-object --enable-linker-build-id --with-linker-hash-style=gnu --enable-languages=all --enable-plugin --enable-initfini-array   --enable-gnu-indirect-function --with-tune=generic 

../configure  --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit  --enable-gnu-unique-object  --with-linker-hash-style=gnu --enable-languages=all --enable-plugin --enable-initfini-array    --enable-gnu-indirect-function --with-tune=generic
```
:::alert-warning
注意:配置得时候在源码下一级目录进行配置. 否则会出现bug.
:::

详情请见<配置>章节.
# 4. 编译:
```bash
make -j 20  VERBOSE=1  > build.log 2>&1 &
```

:::alert-warning
如果cpu 核心数多的话,可以加并发. 详情请见编译.
:::
# 5. 安装
```bash
cd src/bld
make&&install
```
详情请见安装.
# 6. 配置
```bash
cd /usr/bin
ln -sf /usr/local/bin/gcc cc
```
