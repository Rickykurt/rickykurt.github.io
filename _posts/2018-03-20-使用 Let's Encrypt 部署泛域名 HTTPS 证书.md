---
layout: post
title: 使用 Let's Encrypt 部署泛域名 HTTPS 证书
category: Linux
tags: Let's Encrypt Let'sEncrypt letsencrypt https nginx certbot openssl systemd.timer systemd timer 泛域名 证书
---

#使用 Let's Encrypt 部署泛域名 HTTPS 证书

部署时间2018-03-20

## 前置要求

1. 操作系统基于 CentOS 7

   查看系统版本

   ```shell
   $ cat /etc/redhat-release
   ```

   > CentOS Linux release 7.2.1511 (Core)

   ​

2. [使用 Nginx 官方源安装 Nginx](http://nginx.org/en/linux_packages.html)

   ​

3. [使用 Certbot 安装 Let's Encrypt 证书](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)

   ​

4. 如果以前安装过 Certbot 最好执行一下升级。升级后版本是 `0.22.0`

   ```shell
   $ yum update certbot-nginx
   ```

   如果提示已是最新的话就执行下面的命令

   ```shell
   $ yum install certbot-nginx
   $ certbot --version
   ```

   > certbot 0.22.0

   ​

5. 必须升级 OpenSSL 。升级后是 `OpenSSL 1.0.2k-fips  26 Jan 2017`

   必须是1.0.2以上才可以，否则 http2 hsts 等新特性都无法使用

   ```shell
   $ yum update openssl
   $ openssl version
   ```

   > OpenSSL 1.0.2k-fips  26 Jan 2017

   ​

6. 最好升级 Nginx 至最新版，老版 Nginx 通过 yum 安装时有可能不是基于 OpenSSL 1.0.2 进行编译的。

   ```shell
   $ yum update nginx
   ```

   出现 built with OpenSSL 1.0.2k-fips  26 Jan 2017 即说明成功。

   ```shell
   $ nginx -V
   ```

   > nginx version: nginx/1.12.2
   > built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
   > **built with OpenSSL 1.0.2k-fips  26 Jan 2017**
   > TLS SNI support enabled
   > configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx...

## 部署基于 Let's Encrypt 支持泛域名的 HTTPS 证书

1. 通过 Certbot 申请

   ```shell
   $ certbot certonly -d "*.hocyun.cn" -d hocyun.cn --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
   ```

   > Saving debug log to /var/log/letsencrypt/letsencrypt.log
   > Plugins selected: Authenticator manual, Installer None

   - 输入邮箱，以便发送续更提醒邮件，以及安全提醒邮件（必须填写邮箱 ）

   > Enter email address (used for urgent renewal and security notices) (Enter 'c' to
   > cancel): 1519651351@qq.com
   > Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

   - 输入 A 同意服务条款 （必须选 A ）

   > Please read the Terms of Service at
   > https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
   > agree in order to register with the ACME server at https://acme-v02.api.letsencrypt.org/directory
   >
   > (A)gree/(C)ancel: A

   - 输入 N 不共享自己的邮件地址

   > Would you be willing to share your email address with the Electronic Frontier
   > Foundation, a founding partner of the Let's Encrypt project and the non-profit
   > organization that develops Certbot? We'd like to send you email about EFF and 
   >
   > our work to encrypt the web, protect its users and defend digital rights.
   >
   > (Y)es/(N)o: N
   > Obtaining a new certificate
   > Performing the following challenges:
   > dns-01 challenge for hocyun.cn
   > dns-01 challenge for hocyun.cn

   - 输入 Y 绑定服务器 IP （必须选 Y ）

   > NOTE: The IP of this machine will be publicly logged as having requested this
   > certificate. If you're running certbot in manual mode on a machine that is not
   > your server, please ensure you're okay with that.
   >
   > Are you OK with your IP being logged?
   >
   > (Y)es/(N)o: Y

   - 给域名添加 TXT 记录，添加一条指向 _acme-challenge.hocyun.cn 的 TXT 记录，值是 RHjyzMJeiixFwtPBvKBToUoNx9cIhi_c0Cvv0nbno18 一定要等解析生效再敲回车，查看解析是否生效使用 dig -t txt _acme-challenge.hocyun.cn @8.8.8.8 

   > Please deploy a DNS TXT record under the name
   > _acme-challenge.hocyun.cn with the following value:
   >
   > RHjyzMJeiixFwtPBvKBToUoNx9cIhi_c0Cvv0nbno18
   >
   > Before continuing, verify the record is deployed.
   >
   > Press Enter to Continue

   - 给域名添加 TXT 记录，添加一条指向 _acme-challenge.hocyun.cn 的 TXT 记录，值是 gWI1SPVRXNUpsabOrfU0zSwxGiOW9Ai8unmlN7u3gKc 一定要等解析生效再敲回车，查看解析是否生效使用如下命令：

   ```shell
   $ dig -t txt _acme-challenge.hocyun.cn @8.8.8.8 
   ```

   > Please deploy a DNS TXT record under the name
   > _acme-challenge.hocyun.cn with the following value:
   >
   > gWI1SPVRXNUpsabOrfU0zSwxGiOW9Ai8unmlN7u3gKc
   >
   > Before continuing, verify the record is deployed.
   >
   > Press Enter to Continue
   > Waiting for verification...
   > Cleaning up challenges

   - 看到下面的文字就是成功了，证书都在这个路径下面：/etc/letsencrypt/live/hocyun.cn-0001/

   > IMPORTANT NOTES:
   >
   > - Congratulations! Your certificate and chain have been saved at:
   >   /etc/letsencrypt/live/hocyun.cn-0001/fullchain.pem
   >   Your key file has been saved at:
   >   /etc/letsencrypt/live/hocyun.cn-0001/privkey.pem
   >   Your cert will expire on 2018-06-18. To obtain a new or tweaked
   >   version of this certificate in the future, simply run certbot
   >   again. To non-interactively renew *all* of your certificates, run
   >   "certbot renew"
   >
   > - Your account credentials have been saved in your Certbot
   >   configuration directory at /etc/letsencrypt. You should make a
   >   secure backup of this folder now. This configuration directory will
   >   also contain certificates and private keys obtained by Certbot so
   >   making regular backups of this folder is ideal.
   >
   > - If you like Certbot, please consider supporting our work by:
   >
   >   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   >   Donating to EFF:                    https://eff.org/donate-le

   ​

2. 检查证书是否是泛域名，出现 *.hocyun.cn 即是成功

   ```shell
   $ openssl x509 -in  /etc/letsencrypt/live/hocyun.cn-0001/fullchain.pem -noout -text
   ```

   > X509v3 Subject Alternative Name:
   >
   > ​            DNS:*.hocyun.cn, DNS:hocyun.cn

   ​

3. 批量替换原 Nginx 配置文件中的证书地址

   ```shell
   $ cd /etc/nginx/conf.d
   $ sed -i 's/etc\/letsencrypt\/live\/www.hocyun.cn/etc\/letsencrypt\/live\/hocyun.cn-0001/g' *.conf
   ```

4. Let's Encrypt 的 HTTPS 证书有效期只有90天，需要在即将到期时手动更新，这里借助 Systemd.timer 以及 Certbot 自动创建的 Systemd 服务进行自动更新（renew or renewal）

   1. 查看 certbot 自动更新是否启用

      ```shell
      $ systemctl is-enabled certbot-renew.timer
      ```

      > enabled

   2. 启用 certbot 自动更新

      ```shell
      $ systemctl enable certbot-renew.timer
      ```

   3. 查看 certbot 自动更新是否运行

      ```shell
      $ systemctl list-timers
      ```

      > NEXT                         LEFT     LAST                         PASSED  UNIT                         ACTIVATES
      > 三 2018-03-21 18:23:47 CST  9h left  二 2018-03-20 18:23:47 CST  14h ago systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service
      > 四 2018-03-22 00:00:00 CST  15h left 三 2018-03-21 00:00:00 CST  8h ago  certbot-renew.timer          certbot-renew.service
      >
      >  
      >
      > 2 timers listed.
      > Pass --all to see loaded but inactive timers, too.

   4. 启动 certbot 自动更新

      ```shell
      $ systemctl start certbot-renew
      ```

      ​



<p>我的vultr连接<a href="http://www.vultr.com/?ref=6825170">http://www.vultr.com/?ref=6825170</a></p>

<p>{{ page.date | date_to_string }}</p>