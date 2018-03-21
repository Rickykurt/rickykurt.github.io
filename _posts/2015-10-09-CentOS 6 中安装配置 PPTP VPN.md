---
layout: default
title: CentOS 6 中安装配置 PPTP VPN
category : Linux
tags : [Linux, CentOS, PPTP, VPN]
---
## CentOS 6 中安装配置 PPTP VPN

本篇文章主要阐述了在 CentOS 6 上通过yum安装`pptpd`服务来配置 PPTP VPN。

### 安装VPN服务

通过一下命令安装应用程序

    
    yum install -y pptpd

配置 PPTP。对pptpd服务来说，“ppp” 配置文件是非常重要的。首先，将`/etc/ppp/options.pptpd`文件改成如下配置。你可能需要修改 DNS server 的地址。

    
    name pptpd  
    refuse-pap  
    refuse-chap  
    refuse-mschap  
    require-mschap-v2  
    require-mppe-128  
    proxyarp  
    lock  
    nobsdcomp  
    novj  
    novjccomp  
    nologfd  
    ms-dns 8.8.8.8  
    ms-dns 8.8.4.4  

然后，给 PPTP VPN 服务添加账户。编辑`/etc/ppp/chap-secrets`文件，改成如下配置，你可以修改其中的 IP 地址段的范围。

    
    option /etc/ppp/options.pptpd  
    logwtmp  
    localip 192.168.80.1  
    remoteip 192.168.80.101-200  

最后，编辑`/etc/sysctl.conf`开启 IP forwarding。

    
    sed -i 's/^net.ipv4.ip_forward.*/net.ipv4.ip_forward = 1/g' /etc/sysctl.conf  
    sysctl -p  

通过 iptables 修改路由。

    
    iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 1723 -j ACCEPT  
    iptables -t nat -A POSTROUTING -o eth0 -s 192.168.80.0/24 -j MASQUERADE  
    service iptables save  
    service iptables start  

启动服务。

    
    service pptpd start  

好了，现在 PPTP 服务已经安装完成，马上使用 PC 或者手机等其他设备链接 PPTP VPN 吧。

-

<p>我的vultr连接<a href="http://www.vultr.com/?ref=6825170">http://www.vultr.com/?ref=6825170</a></p>

<p>{{ page.date | date_to_string }}</p>