---
layout: default
title: 通过 yum 或者 apt-get 安装最新稳定版 Nginx
---
##通过 yum 或者 apt-get 安装最新稳定版 Nginx


目前，nginx 安装包支持如下发行版：

RHEL/CentOS:


|版本|平台支持|
|---|---|
|5.x|x86_64, i386|
|6.x|x86_64, i386|
|7.x|x86_64|


Debian:


|版本|版本代号|平台支持|
|---|---|---|
|6.x|squeeze|x86_64, i386|
|7.x|wheezy|x86_64, i386|
|8.x|jessie|x86_64, i386|


Ubuntu:


|版本|版本代号|平台支持|
|---|---|---|
|10.04|lucid|x86_64, i386|
|12.04|precise|x86_64, i386|
|14.04|trusty|x86_64, i386, aarch64/arm64|
|14.10|utopic|x86_64, i386|


SLES:


|版本|平台支持|
|---|---|
|12|x86_64|


配置 RHEL/CentOS 发行版的 yum ， Debian/Ubuntu 发行版的 apt 以及SLES的 zypper 从而开启 Linux 程序安装包的自动升级

###稳定版预编译包

在下面的列表中选择对应的 nginx-release 包，从而配置 RHEL/CentOS 的 yum 仓库。

- [RHEL 5](http://nginx.org/packages/rhel/5/noarch/RPMS/nginx-release-rhel-5-0.el5.ngx.noarch.rpm)
- [RHEL 6](http://nginx.org/packages/rhel/6/noarch/RPMS/nginx-release-rhel-6-0.el6.ngx.noarch.rpm)
- [RHEL 7](http://nginx.org/packages/rhel/7/noarch/RPMS/nginx-release-rhel-7-0.el7.ngx.noarch.rpm)
- [CentOS 5](http://nginx.org/packages/centos/5/noarch/RPMS/nginx-release-centos-5-0.el5.ngx.noarch.rpm)
- [CentOS 6](http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm)
- [CentOS 7](http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm)

这个程序包包含了 yum 的配置文件以及 RPMs 认证签名所需要的公共 PGP 密钥。下载并安装这个包，然后执行以下命令：

> yum install nginx

当然也可以通过手动方式配置仓库（不通过珊 Gian的安装 nginx-release 包程序的方式）。创建如下文件 /etc/yum.repos.d/nginx.repo 并且写入如下内容：

> [nginx]  
> name=nginx repo  
> baseurl=http://nginx.org/packages/OS/OSRELEASE/$basearch/  
> gpgcheck=0  
> enabled=1

根据发行版的名字把 “OS” 替换成 “rhel” 或者 “centos”，并且把“OSRELEASE”替换为“5”或者“6”或者“7”，分别对应 5.x，6.x，7.x 的版本。

对于 Debian/Ubuntu 发行版，在安装 nginx 程序时为了认证 nginx 仓库签名并且排除丢失 PGP 密钥的警告错误，需要给 apt 程序的钥匙串添加一个 nginx 程序签名的密钥。下载这个 [密钥](http://nginx.org/keys/nginx_signing.key) ，并且通过以下命令进行安装。

> sudo apt-key add nginx_signing.key

对于 Debian 发行版，需要将下面代码中的发行版代号 *codename* 替换为你当前的[发行版代号](http://nginx.org/en/linux_packages.html#distributions)，并且写入到 /etc/apt/sources.list 文件中：

> deb http://nginx.org/packages/mainline/debian/ *codename* nginx   
> deb-src http://nginx.org/packages/mainline/debian/ *codename* nginx

对于 Ubuntu 发行版，需要将下面代码中的发行版代号 *codename* 替换为你当前的[发行版代号](http://nginx.org/en/linux_packages.html#distributions)，并且写入到 /etc/apt/sources.list 文件中：

> deb http://nginx.org/packages/mainline/ubuntu/ *codename* nginx  
> deb-src http://nginx.org/packages/mainline/ubuntu/ *codename* nginx

对于 Debian/Ubuntu 发行版，执行如下命令：

> apt-get update  
> apt-get install nginx

对于 SLES 发行版，执行如下命令即可：

> zypper addrepo -G -t yum -c 'http://nginx.org/packages/mainline/sles/12' nginx


### 参数配置列表

稳定版 nginx 二进制预编译包有如下配置参数：

> --prefix=/etc/nginx  
> --sbin-path=/usr/sbin/nginx  
> --conf-path=/etc/nginx/nginx.conf  
> --error-log-path=/var/log/nginx/error.log  
> --http-log-path=/var/log/nginx/access.log  
> --pid-path=/var/run/nginx.pid  
> --lock-path=/var/run/nginx.lock  
> --http-client-body-temp-path=/var/cache/nginx/client\_temp  
> --http-proxy-temp-path=/var/cache/nginx/proxy\_temp  
> --http-fastcgi-temp-path=/var/cache/nginx/fastcgi\_temp  
> --http-uwsgi-temp-path=/var/cache/nginx/uwsgi\_temp  
> --http-scgi-temp-path=/var/cache/nginx/scgi\_temp  
> --user=nginx  
> --group=nginx  
> --with-http\_ssl\_module  
> --with-http\_realip\_module  
> --with-http\_addition\_module  
> --with-http\_sub\_module  
> --with-http\_dav\_module  
> --with-http\_flv\_module  
> --with-http\_mp4\_module  
> --with-http\_gunzip\_module  
> --with-http\_gzip\_static\_module  
> --with-http\_random\_index\_module  
> --with-http\_secure\_link\_module  
> --with-http\_stub\_status\_module  
> --with-http\_auth\_request\_module  
> --with-mail  
> --with-mail\_ssl\_module  
> --with-file-aio  
> --with-http\_spdy_module  
> --with-ipv6  

开发版 nignx 有以下配置参数：

> --with-threads  
> --with-stream  
> --with-stream\_ssl_module  

以及

> --with-http\_spdy_module  

现在替换为

> --with-http\_v2_module

程序报已经编译了所有的模块，并且不需要任何其他的库以避免额外的依赖关系。

###签名

RPM 以及 Debian／Ubuntu 的程序仓库都使用了数字签名来验证程序报的完整性和原始性。下载[ nginx 签名密钥](http://nginx.org/keys/nginx_signing.key)并导入到 rpm 或者 apt 程序中，来检查数字签名：

- Debian/Ubuntu:
> sudo apt-key add nginx_signing.key

- RHEL/CentOS:
> sudo rpm --import nginx_signing.key

- SLES:
> sudo rpm --import nginx_signing.key

Debian/Ubuntu/SLES 发行版默认会检查数字签名，但是对于 RHEL/CentOS 发行版，需要修改 /etc/yum.repos.d/nginx.repo 文件中的参数为如下内容：

> gpgcheck=1

### 未翻译部分
Since our PGP keys and packages are located on the same server, they are equally trusted. It is highly advised to additionally verify the authenticity of the downloaded PGP key. PGP has the “Web of Trust” concept, when a key is signed by someone else’s key, that in turn is signed by another key and so on. It often makes possible to build a chain from an arbitrary key to someone’s key who you know and trust personally, thus verify the authenticity of the first key in a chain. This concept is described in details in GPG Mini Howto. Our keys have enough signatures, and their authenticity is relatively easy to check.

<p>我的vultr连接<a href="http://www.vultr.com/?ref=6825170">http://www.vultr.com/?ref=6825170</a>

<p>{{ page.date | date_to_string }}</p>