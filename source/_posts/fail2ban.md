---
title: fail2ban
date: 2017-07-10 11:03:01
tags:
---
转自： http://www.2cto.com/Article/201406/310910.html 

1.fail2ban简介：

fail2ban可以监视你的系统日志，然后匹配日志的错误信息（正则式匹配）执行相应的屏蔽动作（一般情况下是调用防火墙屏蔽），如:当有人在试探你的SSH、SMTP、FTP密码，只要达到你预设的次数，fail2ban就会调用防火墙屏蔽这个IP，而且可以发送e-mail通知系统管理员，是一款很实用、很强大的软件！其实fail2ban就是用来监控，具体是调用iptables来实现动作！

2.在centos上面安装fail2ban监控nginx日志。
<!-- more --> 

一、首先是服务安装
首先配置yum源，这里采用的是yum直接装（也可源码安装）
vim /etc/yum.repos.d/CentOS-Base.repo
在最后新增：

[atrpms]
name=Red Hat Enterprise Linux $releasever - $basearch - ATrpms
baseurl=http://dl.atrpms.net/el$releasever-$basearch/atrpms/stable
gpgkey=http://ATrpms.net/RPM-GPG-KEY.atrpms
gpgcheck=1
enabled=1
然后直接就yum装：yum -y install fail2ban
安装完成后，服务配置目录为：/etc/fail2ban
 

/etc/fail2ban/action.d                #动作文件夹，内含默认文件。iptables以及mail等动作配置
/etc/fail2ban/fail2ban.conf           #定义了fai2ban日志级别、日志位置及sock文件位置
/etc/fail2ban/filter.d                #条件文件夹，内含默认文件。过滤日志关键内容设置
/etc/fail2ban/jail.conf               #主要配置文件，模块化。主要设置启用ban动作的服务及动作阀值
/etc/rc.d/init.d/fail2ban             #启动脚本文件
二、安装后配置
首先来看看日志文件的默认定义：
cat /etc/fail2ban/fail2ban.conf |grep -v ^#

[Definition]
loglevel = 3
logtarget = SYSLOG              #我们需要做的就是把这行改成/var/log/fail2ban.log，方便用来记录日志信息
socket = /var/run/fail2ban/fail2ban.sock

再来看看主配置默认生效的配置：
cat /etc/fail2ban/jail.conf |grep -v ^# |less

[DEFAULT]                                 #全局设置
ignoreip = 127.0.0.1                      #忽略的IP列表,不受设置限制（白名单）
bantime  = 600                            #屏蔽时间，单位：秒
findtime  = 600                           #这个时间段内超过规定次数会被ban掉
maxretry = 3                              #最大尝试次数
backend = auto                            #日志修改检测机制（gamin、polling和auto这三种）
 
[ssh-iptables]                            #针对各服务的检查配置，如设置bantime、findtime、maxretry和全局冲突，服务优先级大于全局设置
enabled  = true                           #是否激活此项（true/false）
 
filter   = sshd                           #过滤规则filter的名字，对应filter.d目录下的sshd.conf
action   = iptables[name=SSH, port=ssh, protocol=tcp]                                                                        #动作的相关参数
sendmail-whois[name=SSH, dest=root, sender=fail2ban@example.com]   #触发报警的收件人
logpath  = /var/log/secure                #检测的系统的登陆日志文件
maxretry = 5                              #最大尝试次数
PS：logpath(Centos5和Rhel5中)要写成/var/log/secure，这个是系统登陆日志，不能随意设置

service fail2ban start                     #启动服务即可（就用默认的主配置文件里定义的）
service iptables start                     #fail2ban依赖预iptables                                                    #之前改过日志路径，不行的话就再重启fail2ban

三、扩展说明
其实fail2ban的功能还是很丰富的，刚刚只是测试了它的防ssh暴力破解功能。
下面简单提下我用的一些功能：
本人是用在邮件服务器上，所以会监控pop、http等服务，具体配置见下（不做演示了）

[pop3]
enabled = true
filter   = courierlogin
action   = iptables[name=pop3, port=110, protocol=tcp]
logpath = /var/log/maillog
bantime = 1800
findtime = 300
maxretry = 30
 
[webmail]
enabled = true
filter   = webmail
action   = iptables[name=httpd, port=http, protocol=tcp]
logpath = /var/log/maillog
bantime = 900
findtime = 300
maxretry = 5
五.利用fail2ban监控nginx的日志：

首先在/etc/fail2ban/jail.conf里加入

[yunvn-get-dos]
enabled = true
port = http,https
filter = nginx-bansniffer
action = iptables[name=IT300, port=http, protocol=tcp]
sendmail-whois[name=IT300, dest=xxxxx@qq.com, sender=xxxxxx@163.com]
logpath = /home/wwwlogs/yunvn.log
maxretry = 300
findtime = 60
bantime = 3600

然后创建文件/etc/fail2ban/filter.d/nginx-bansniffer.conf，内容如下：

[Definition]
failregex = <HOST> -.*- .*HTTP/1.* .* .*$
ignoreregex =

最后重启fail2ban服务即可，在上面的配置中，我们对每60秒有超过300次访问的ip，封禁1小时。


