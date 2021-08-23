---
title: curl
description: curl
published: true
date: 2021-08-23T03:40:12.211Z
tags: curl
editor: markdown
dateCreated: 2020-08-01T15:54:58.231Z
---

# curl
curl是一个利用URL规则在命令行下工作的文件传输工具。它支持文件的上传和下载，所以是综合传输工具，但按传统，习惯称url为下载工具。
```
格式: curl [options...] <url>
Options: (H) 仅表示HTTP/HTTPS 协议, (F) 仅表示 FTP 协议

--abstract-unix-socket <path> 通过抽象的Unix域套接字连接
--anyauth 选择任何身份验证方法（H）
-a, --append 上传时附加到目标文件（F/SFTP）
    --basic 使用HTTP基本身份验证（H）
    --cacert <file> 用于验证对等端的CA证书（SSL）
    --capath <dir> 用于验证对等机的CA目录（SSL）
-E, --cert CERT[:PASSWD] 客户端证书文件和密码（SSL）
    --cert-status 验证服务器证书的状态（SSL）
    --cert-type <type> 证书文件类型（der/pem/eng）（SSL）
    --ciphers <list of ciphers> 要使用的SSL密码（SSL）
    --compressed 请求压缩响应（使用deflate或gzip）
    --compressed-ssh 启用ssh压缩
-K, --config <file> 指定要读取的配置文件
    --connect-timeout <seconds> 连接允许的最长时间
    --connect-to <HOST1:PORT1:HOST2:PORT2> 连接到主机
-C, --continue-at OFFSET 恢复传输偏移量
-b, --cookie <data> 从 string/file 发送 cookie（H）
-c, --cookie-jar <filename> 操作后将cookie写入<filename>
    --create-dirs 创建必要的本地目录层次结构
    --crlf 上载时将 LF 转换为 CRLF
    --crlfile <file> 从给定的文件获取PEM格式的CRL列表
-d, --data <data> HTTP Post 数据（H）
    --data-ascii <data> HTTP POST ASCII 数据（H）
    --data-binary <data> HTTP POST 二进制数据（H）
    --data-raw <data> HTTP POST data, 允许 '@' 字符（H）
    --data-urlencode <data> HTTP POST url 编码数据（H）
    --delegation <LEVEL> GSS-API授权权限
    --digest 使用HTTP摘要身份验证（H）
-q, --disable 禁用.currlc
    --disable-eprt 禁止使用EPRT或LPRT（F）
    --disable-epsv 禁止使用EPSV（F）
    --dns-interface <interface> 用于DNS请求的接口
    --dns-ipv4-addr <address> 用于DNS请求的IPv4地址
    --dns-ipv6-addr <address> 用于DNS请求的IPv6地址
    - -dns-servers <addresses> 要使用的DNS服务器地址
-D, --dump-header <filename> 将接收到的头写入<filename>
    --egd-file <file> 随机数据的EGD套接字路径（SSL）
    --engine <name> 要使用的加密引擎
    --expect100-timeout <seconds> 要等多久才能100继续
-f, --fail HTTP错误时不显示（完全没有输出）（H）
    --fail-early 第一次传输失败，不继续
    --false-start 启动 TLS=False 启动
-F, --form <name=content> 指定HTTP多部分发布数据(H)
    --form-string <name=string> 指定多部分MIME数据
    --ftp-account <data> 帐户数据字符串(F)
    --ftp-alternative-to-user <command> 替换用户的字符串[名称](F)
    --ftp-create-dirs 创建远程目录（如果不存在）(F)
    --ftp-method <method> 控制CWD使用(F)
    --ftp-pasv 使用pasv/epsv而不是port(F)
-P, --ftp-port <address> 使用 PORT 而不是PASV(F)
    --ftp-pret 在pasv之前发送pret(F)
    --ftp-skip-pasv-ip 跳过PASV的IP地址(F)
    --ftp-ssl-ccc 认证后发送CCC(F)
    --ftp-ssl-ccc-mode <active/passive> 设置CCC模式(F)
    --ftp-ssl-control ftp登录需要ssl/tls，传输清除(F)
-G, --get 以get的方式来发送数据（H）
-g, --globoff 使用{} 和 [] 禁用URL序列和范围
    --happy-eyeballs-timeout-ms 尝试ipv4之前等待ipv6的时间（以毫秒计）
-I, --head 仅显示响应首部信息
    --haproxy-protocol 发送haproxy代理协议头
-H, --header <header/@file> 将自定义头传递到服务器
-h, --help 显示当前帮助文档
    --hostpubmd5 <md5> 主机公钥的可接受MD5哈希(SSH)
-0, --http1.0 使用 HTTP 1.0（H）
    --http1.1 使用 HTTP 1.1
    --http2 使用 HTTP 2
    --http2-prior-knowledge 使用HTTP 2而不升级HTTP/1.1
    --ignore-content-length 忽略远程资源的大小
-i, --include 在输出中包含协议响应头(H/F)
-k, --insecure 使用SSL时允许不安全的服务器连接（H）
    --interface <name> 使用网络接口（或地址）
-4, --ipv4 将名称解析为IPv4地址
-6, --ipv6 将名称解析为IPv6地址
-j, --junk-session-cookies 忽略从文件读取的会话cookie（H）
    --keepalive-time <seconds> 保持探针的间隔时间
    --key <key> 私钥文件名(SSL/SSH)
    --key-type <type> 私钥文件类型 (DER/PEM/ENG)(SSL)
    --krb <level> 启用具有安全性的Kerberos<level>(F)
    --libcurl <file> 转储此命令行的libcurl等效代码
    --limit-rate <speed> 限制传输速率
    -l, --list-only 仅列出ftp目录的名称(F)
    --local-port <num/range> 强制使用本地端口号的范围
-L, --location 跟踪重定向(H)
    --location-trusted 像--location样,并将auth发送到其他主机(H)
    --login-options <options> 服务器登录选项
    --mail-auth <address> 原始电子邮件的发起人地址
    --mail-from <address> 来自此地址的邮件
    --mail-rcpt <address> 邮寄到此地址
-M, --manual 显示curl完整手册
    --max-filesize <bytes> 要下载的最大文件大小
    --max-redirs <num> 允许的最大重定向数
-m, --max-time <seconds> 允许传输的最长时间
    --metalink 将给定的URL作为metalink xml文件处理
    --negotiate 使用HTTP协商（SPNEGO）身份验证
-n, --netrc 必须读取.netrc以获取用户名和密码
    --netrc-file <filename> 为netrc指定文件
    --netrc-optional 使用.netrc或url
-:, --next 使下一个URL使用其单独的选项集
    --no-alpn 禁用ALPN TLS扩展
-N, --no-buffer 禁用输出流的缓冲
    --no-keepalive 在连接上禁用tcp keepalive
    --no-npn 禁用NPN TLS扩展
    --no-sessionid 禁用SSL会话ID重用
    --noproxy <no-proxy-list> 不使用代理的主机列表
    --ntlm 使用HTTP NTLM身份验证
    --ntlm-wb 对WinBind使用HTTP NTLM身份验证
    --oauth2-bearer <token> OAuth 2 Bearer Token
-o, --output <file> 写入文件而不是stdout
    --pass <phrase> 私钥的密码短语
    --path-as-is 不要挤压……URL路径中的序列
    --pinnedpubkey <hashes> 文件/哈希 用于验证对等机的公钥
    --post301 在301重定向后不要切换到GET
    --post302 在302重定向后不要切换到GET
    --post303 在303重定向后不要切换到GET
    --preproxy [protocol://]host[:port] 首先使用此代理
-#, --progress-bar 将传输进度显示为条形图
    --proto <protocols> 启用/禁用协议
    --proto-default <protocol> 对任何缺少方案的URL使用协议
    --proto-redir <protocols> 在重定向时启用/禁用协议
-x, --proxy [protocol://]host[:port] 使用此代理
    --proxy-anyauth 选择任何代理身份验证方法 (H)
    --proxy-basic 在代理上使用基本身份验证(H)
    --proxy-cacert <file> 用于验证对等代理的CA证书(H)
    --proxy-capath <dir> 用于验证代理对等机的CA目录(H)
    --proxy-cert <cert[:passwd]> 设置代理的客户端证书(H)
    --proxy-cert-type <type> HTTS代理的客户端证书类型(H)
    --proxy-ciphers <list> 用于代理的SSL密码
    --proxy-crlfile <file> 为代理设置一个CRL列表
    --proxy-digest 在代理上使用摘要式身份验证
    --proxy-header <header/@file> 将自定义头传递给代理
    --proxy-insecure 在不验证代理的情况下执行HTTPS代理连接
    --proxy-key <key> HTTPS代理的私钥
    --proxy-key-type <type> 代理的私钥文件类型
    --proxy-negotiate 在代理上使用HTTP协商（SPNEGO）身份验证
    --proxy-ntlm 在代理上使用NTLM身份验证
    --proxy-pass <phrase> https代理的私钥的密码短语
    --proxy-pinnedpubkey <hashes> 用于验证代理的公钥的 文件/哈希
    --proxy-service-name <name> SPNEGO代理服务名称
    --proxy-ssl-allow-beast 允许HTTPS代理的互操作存在安全缺陷
    --proxy-tlsauthtype <type> HTTPS代理的TLS身份验证类型
    --proxy-tlspassword <string> HTTPS代理的TLS密码
    --proxy-tlsuser <name> HTTPS代理的TLS用户名
    --proxy-tlsv1 将tlsv1用于HTTPS代理
-U, --proxy-user <user:password> 代理用户和密码
    --proxy1.0 <host[:port]> 在给定端口上使用HTTP/1.0代理
-p, --proxytunnel 通过HTTP代理隧道操作（使用connect）
    --pubkey <key>  ssh公钥文件名
-Q, --quote <cmd> 传输前将命令发送到服务器
    --random-file <file> 从文件中读取随机数据(SSL)
-r, --range <range> 仅检索范围内的字节
    --raw 执行http“raw”；无传输解码(H)
-e, --referer <URL> 引用URL(H)
-J, --remote-header-name 使用header提供的文件名
-O, --remote-name 将输出写入名为远程文件的文件
    --remote-name-all 对所有URL使用远程文件名
-R, --remote-time 在本地输出上设置远程文件的时间
-X, --request <command> 指定要使用的请求命令
    --request-target 指定此请求的目标
    --resolve <host:port:address> 将主机+端口解析为此地址
    --retry <num> 如果出现暂时性问题，请重试请求
    --retry-connrefused 拒绝连接时重试（与--retry一起使用）
    --retry-delay <seconds> 两次重试之间的等待时间
    --retry-max-time <seconds> 仅在此期间内重试
    --sasl-ir 在SASL身份验证中启用初始响应
    --service-name <name> SPNEGO服务名称
-S, --show-error 显示错误,即使使用-s
-s, --silent 静音模式
    --socks4 <host[:port]> 指定主机+端口上的socks4代理
    --socks4a <host[:port]> 指定主机+端口上的socks4a代理
    --socks5 <host[:port]> 指定主机+端口上的socks5代理
    --socks5-basic 为socks5代理启用用户名/密码验证
    --socks5-gssapi 为socks5代理启用GSS-API身份验证
    --socks5-gssapi-nec 与NEC Socks5服务器的兼容性
    --socks5-gssapi-service <name> GSS-API的socks5代理服务名称
    --socks5-hostname <host[:port]> socks5代理，将主机名传递给代理
-Y, --speed-limit <speed> 停止比此慢的传输
-y, --speed-time <seconds> 在此时间后触发“速度限制”中止
    --ssl 尝试ssl/tls (FTP, IMAP, POP3, SMTP)
    --ssl-allow-beast 允许安全缺陷改进互操作
    --ssl-no-revoke 禁用证书吊销检查（WinSL）
    --ssl-reqd 需要SSL/TLS(FTP, IMAP, POP3, SMTP)
-2, --sslv2 使用SSLv2(SSL)
-3, --sslv3 使用SSLv3(SSL)
    --stderr 重定向stderr的位置
    --suppress-connect-headers 禁止代理连接响应头(SSL)
    --tcp-fastopen 使用TCP快速打开
    --tcp-nodelay 使用tcp_nodelay选项
-t, --telnet-option <opt=val> 设置telnet选项
    --tftp-blksize <value> 设置tftp blksize选项
    --tftp-no-options 不要发送任何TFTP选项
-z, --time-cond <time> 基于时间条件的传输
    --tls-max <VERSION> 使用TLSV1.0或更高版本
    --tlsauthtype <type> TLS身份验证类型
    --tlspassword  TLS密码
    --tlsuser <name> TLS用户名
-1, --tlsv1 使用TLSV1.0或更高版本(SSL)
    --tlsv1.0 使用TLSv1.0(SSL)
    --tlsv1.1 使用TLSv1.1(SSL)
    --tlsv1.2 使用TLSv1.2(SSL)
    --tlsv1.3 使用TLSv1.3(SSL)
    --tr-encoding 请求压缩传输编码(H)
    --trace <file> 将调试跟踪写入文件
    --trace-ascii <file> 如 --trace,但无十六进制输出
    --trace-time 向跟踪/详细输出添加时间戳
    --unix-socket <path> 通过这个Unix域套接字连接
-T, --upload-file <file> 将本地文件传输到目标
    --url <url> 要使用的URL
-B, --use-ascii 使用ASCII/文本传输
-u, --user <user:password> 服务器用户和密码
-A, --user-agent <name> 将用户代理<name>发送到服务器
-v, --verbose 打印操作的详情信息
-V, --version 显示 curl 的版本号
-w, --write-out <format> 完成后使用输出格式
    --xattr 将元数据存储在扩展文件属性中

```
# 快速使用教程
从Netscape网站获取主页
```bash
curl http://www.netscape.com/
```
从ftp-server 上下载文件
```bash
curl ftp://ftp.funet.fi/README
```
使用8000 端口获取web 页面
```bash
curl http://www.weirdserver.com:8000/
```
获取ftp 网站目录列表
```bash
curl ftp://cool.haxx.se/
```
同时下载两个文档
```bash
curl ftp://cool.haxx.se/ http://www.weirdserver.com:8000/
```
从sftp-server 下载
```bash
curl ftps://files.are.secure.com/secrets.txt
```
从sftp-server 下载
```
curl --ftp-ssl ftp://files.are.secure.com/secrets.txt
```
使用SFTP 从ssh 服务器获取一个文件.
```bash
curl -u username sftp://example.com/etc/issue
```
# 例子
  a,抓取页面内容到一个文件中


[root@krlcgcms01 mytest]# curl -o home.html  http://blog.51yip.com 

b,用-O（大写的），后面的url要具体到某个文件，不然抓不下来。我们还可以用正则来抓取东西


[root@krlcgcms01 mytest]# curl -O http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  
[root@krlcgcms01 mytest]# curl -O http://blog.51yip.com/wp-content/uploads/2010/[0-9][0-9]/aaaaa.jpg 

c,模拟表单信息，模拟登录，保存cookie信息


[root@krlcgcms01 mytest]# curl -c ./cookie_c.txt -F log=aaaa -F pwd=****** http://blog.51yip.com/wp-login.php

d,模拟表单信息，模拟登录，保存头信息


[root@krlcgcms01 mytest]# curl -D ./cookie_D.txt -F log=aaaa -F pwd=****** http://blog.51yip.com/wp-login.php 

e,使用cookie文件


[root@krlcgcms01 mytest]# curl -b ./cookie_c.txt  http://blog.51yip.com/wp-admin

f,断点续传，-C(大写的)


[root@krlcgcms01 mytest]# curl -C -O http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg 

-----------------------
g,传送数据,最好用登录页面测试，因为你传值过去后，curl回抓数据，你可以看到你传值有没有成功


[root@krlcgcms01 mytest]# curl -d log=aaaa  http://blog.51yip.com/wp-login.php  

h,显示抓取错误，下面这个例子，很清楚的表明了。


[root@krlcgcms01 mytest]# curl -f http://blog.51yip.com/asdf  
curl: (22) The requested URL returned error: 404  
[root@krlcgcms01 mytest]# curl http://blog.51yip.com/asdf  
i,伪造来源地址，有的网站会判断，请求来源地址。


[root@krlcgcms01 mytest]# curl -e http://localhost http://blog.51yip.com/wp-login.php 

j,当我们经常用curl去搞人家东西的时候，人家会把你的IP给屏蔽掉的,这个时候,我们可以用代理


[root@krlcgcms01 mytest]# curl -x 24.10.28.84:32779 -o home.html http://blog.51yip.com

k,比较大的东西，我们可以分段下载


[root@krlcgcms01 mytest]# curl -r 0-100 -o img.part1 http://blog.51yip.com/wp-

content/uploads/2010/09/compare_varnish.jpg
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
 Dload  Upload   Total   Spent    Left  Speed
100   101  100   101    0     0    105      0 --:--:-- --:--:-- --:--:--     0
[root@krlcgcms01 mytest]# curl -r 100-200 -o img.part2 http://blog.51yip.com/wp-

content/uploads/2010/09/compare_varnish.jpg
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
 Dload  Upload   Total   Spent    Left  Speed
100   101  100   101    0     0     57      0  0:00:01  0:00:01 --:--:--     0
[root@krlcgcms01 mytest]# curl -r 200- -o img.part3 http://blog.51yip.com/wp-

content/uploads/2010/09/compare_varnish.jpg
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
 Dload  Upload   Total   Spent    Left  Speed
100  104k  100  104k    0     0  52793      0  0:00:02  0:00:02 --:--:-- 88961
[root@krlcgcms01 mytest]# ls |grep part | xargs du -sh
4.0K    one.part1
112K    three.part3
4.0K    two.part2

l,不会显示下载进度信息


[root@krlcgcms01 mytest]# curl -s -o aaa.jpg  http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  

m,显示下载进度条


[root@krlcgcms01 mytest]# curl -# -O  http://blog.51yip.com/wp-content/uploads/2010/09/compare_varnish.jpg  
    ######################################################################## 100.0%  

n,通过ftp下载文件


[zhangy@BlackGhost ~]$ curl -u 用户名:密码 -O http://blog.51yip.com/demo/curtain/bbstudy_files/style.css  
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current  
 Dload  Upload   Total   Spent    Left  Speed  
101  1934  101  1934    0     0   3184      0 --:--:-- --:--:-- --:--:--  7136 

o,通过ftp上传


[zhangy@BlackGhost ~]$ curl -T test.sql ftp://用户名:密码@ip:port/demo/curtain/bbstudy_files/  
  
## 例子2
  # 获取http_code

$ curl -m 5 -s -w %{http_code} http://localhost/nginx-status -o /dev/null/span;