---
title: WebSocket+TLS+Web
date: 2018-01-20 15:08:09
tags: v2ray
---


# 0.WebSocket+TLS+Web

这里是个人网站配置HTTPS笔记：)

<!-- more -->
# 1.配置

首先要准备好启用了HTTPS的nginx，然后本地客户端和nginx使用webSocket协议进行连接，nginx监听443端口，将收到的v2ray的数据转发到v2ray进行处理。

## 1.1 证书生成

在生成证书之前当然要先注册一个域名。下面简单介绍三种生成证书的方式：

1.使用acme.sh，具体步骤请看[acme.sh](acme.sh)

2.安装宝塔面板，生成ssl证书

3.在[sslforfree.com](sslforfree.com)生成证书

以上三种方式都是生成免费的Let's Encrypt证书，本质上没有任何区别。

## 1.2 服务器配置
```
{
    "inbound": {
        "port": 10000,
        "listen": "127.0.0.1", //只监听 127.0.0.1，避免除本机外的机器探测到开放了 10000 端口
        "protocol": "vmess",
        "settings": {
            "clients": [
                {
                    "id": "b831381d-6324-4d53-ad4f-8cda48b30811", //改成自己生成的UUID
                    "alterId": 64
                }
            ]
        },
        "streamSettings": {
            "network": "ws",
            "wsSettings": {
                "path": "/ray"
            }
        }
    },
    "outbound": {
        "protocol": "freedom",
        "settings": {}
    }
}
```
## 1.3 Nginx 配置
```
    server {
    	......
     listen 443 ssl;
     ssl on;
     ssl_certificate /etc/v2ray/v2ray.crt;
     ssl_certificate_key /etc/v2ray/v2ray.key;
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
     ssl_ciphers HIGH:!aNULL:!MD5;
     server_name mydomain.me;
     location /ray {
     proxy_redirect off;
     proxy_pass http://127.0.0.1:10000;#假设WebSocket监听在环回地址的10000端口上
     proxy_http_version 1.1;
     proxy_set_header Upgrade $http_upgrade;
     proxy_set_header Connection "upgrade";
     proxy_set_header Host $http_host;
     }
    	......
    	//其余无关配置已省略
    }
```
## 1.4 客户端配置
```
{
    "inbound": {
        "port": 1080,
        "listen": "127.0.0.1",
        "protocol": "socks",
        "settings": {
            "auth": "noauth",
            "udp": false
        }
    },
    "outbound": {
        "protocol": "vmess",
        "settings": {
            "vnext": [
                {
                    "address": "mydomain.me",
                    "port": 443,
                    "users": [
                        {
                            "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
                            "alterId": 64
                        }
                    ]
                }
            ]
        },
        "streamSettings": {
            "network": "ws",
            "security": "tls",
            "tlsSettings": {
                "serverName": "mydomain.me"
            },
            "wsSettings": {
                "path": "/ray"
            }
        }
    }
}
```
# 2.完