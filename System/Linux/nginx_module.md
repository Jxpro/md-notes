# nginx 配置模块化设计

[TOC]

## 一、概述

配置时使用模块化的设计就像给你的 Nginx 装上乐高积木：想拆就拆，想合就合，清爽又安全。

## 二、设计

目录结构

```shell
/etc/nginx/
├── nginx.conf             # 主配置，引入全局设置
├── conf.d/                # 放通用模块，如 gzip、日志格式
│   └── gzip.conf
├── stream.d/
│   └── stream.conf
├── sites-available/       # 放所有站点配置源文件
│   └── example.com.conf
├── sites-enabled/         # 通过软链启用站点
│   └── example.com.conf → ../sites-available/example.com.conf
└── upstream/              # 放负载均衡后端定义
    └── api.upstream.conf
```

在`nginx.conf`中使用 `include` 挂载模块

```nginx
# TCP/UDP 代理层
stream {
    include /etc/nginx/stream.d/*.conf;
}

# HTTP 层
http {
    include       /etc/nginx/conf.d/*.conf;
    include       /etc/nginx/upstream/*.conf;
    include       /etc/nginx/sites-enabled/*.conf;
}
```

这样新增模块只要丢到对应目录，重载一下就生效。

## 三、关键模块

-   上游服务器（upstream）：把所有（非 HTTP）后端节点放 `upstream/api.upstream.conf`；比如数据库。
-   站点配置（server）：每个域名一个文件，放在 `sites-available`。想禁用时就删软链，不用动主配置。
-   公共逻辑（location、rewrite）：如 API 路径、静态资源缓存策略，也可以单独拆出来，按需 include。

## 四、启用与禁用

-   启用：`ln -s /etc/nginx/sites-available/foo.conf /etc/nginx/sites-enabled/`
-   禁用：`rm /etc/nginx/sites-enabled/foo.conf`

然后 `nginx -s reload`，就可以快速生效或失效配置并且保留备份。使用 `nginx -t` 测试测语法；使用 `nginx -T` 查看合并后配置。

## 五、案例

-   `/etc/nginx/nginx.conf`

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

# TCP/UDP 代理层
stream {
    log_format proxy '$remote_addr [$time_local] '
                  '$protocol $status $bytes_sent $bytes_received '
                  '$session_time "$upstream_addr" "$upstream_bytes_sent"'
                  '"$upstream_bytes_received" "$upstream_connect_time"';
    access_log /var/log/nginx/stream.access.log proxy;
    error_log  /var/log/nginx/stream.error.log error;

    include /etc/nginx/stream.d/*.conf;
}

# HTTP 层
http {
	include       /etc/nginx/conf.d/*.conf;        # 基础／SSL／日志／gzip／map
	include       /etc/nginx/upstream/*.conf;      # 如有 upstream
	include       /etc/nginx/sites-enabled/*.conf; # 虚拟主机
}

#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
#
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}

```

### conf.d

-   `/etc/nginx/conf.d/basic.conf`

```nginx
##
# Basic Settings
##

sendfile on;
tcp_nopush on;
tcp_nodelay on;
keepalive_timeout 65;
types_hash_max_size 2048;
# server_tokens off;

# server_names_hash_bucket_size 64;
# server_name_in_redirect off;

include /etc/nginx/mime.types;
default_type application/octet-stream;
```

-   `/etc/nginx/conf.d/ssl.conf`

```nginx
##
# SSL Settings
##

ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
ssl_prefer_server_ciphers on;
ssl_ciphers HIGH:!aNULL:!MD5;

# SSL证书和私钥
ssl_certificate path_to_cert;
ssl_certificate_key path_to_key;
```

-   `/etc/nginx/conf.d/logging.conf`

```nginx
##
# Logging Settings
##

access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;
```

-   `/etc/nginx/conf.d/gzip.conf`

```nginx
##
# Gzip Settings
##

gzip on;

# gzip_vary on;
# gzip_proxied any;
# gzip_comp_level 6;
# gzip_buffers 16 8k;
# gzip_http_version 1.1;
# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

-   `/etc/nginx/conf.d/websocket.conf`

```nginx
##
# WebSocket Settings
##

map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
```

-   `/etc/nginx/conf.d/redirect.conf`

```nginx
##
# Redirect Settings
##

server {
    listen 80;
    server_name .domain.com;
    return 301 https://$host$request_uri;
}
```

### stream.d

-   `/etc/nginx/stream.d/mysql.conf`

```nginx
server{
    # keepalive的可配置参数有以下几个：keepidle，keepintvl，keepcnt
    # keepidle为最长空闲时间；keepintvl为探测间隔时间；keepcnt是探测次数
    # 实际配置的格式为：so_keepalive=on|off|[keepidle]:[keepintvl]:[keepcnt]
    listen 6837 so_keepalive=30m::10;
    proxy_pass rm-xxx.mysql.rds.aliyuncs.com:3306;
}
```

### sites-available

-   `/etc/nginx/sites-available/default_ssl.conf`

```nginx
server {
    listen 443 ssl default_server;
    server_name _; # catch-all

    location / {
        proxy_pass https://cloud.holinality.com/;
        proxy_redirect off;
        proxy_ssl_server_name on;
        sub_filter_once off;
        sub_filter "cloud.holinality.com" $server_name;
        proxy_set_header Host "cloud.holinality.com";
        proxy_set_header Referer $http_referer;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Accept-Encoding "";
        proxy_set_header Accept-Language "zh-CN";
    }
}
```

-   `/etc/nginx/sites-available/cert.domain.com.conf`

```nginx
server {
    listen 443 ssl;
    server_name cert.domain.com;

    location / {
        # MIME 类型适合 PEM 格式
        # default_type application/pem-certificate-chain;
        # 设置内容类型为文本，便于直接显示证书内容
        default_type text/plain;
        # 存放证书文件的路径，需要创建符号链接
        root /etc/nginx/certs;
        # 仅提供 fullchain.cer 文件
        try_files /fullchain.cer =404;

        # 禁用缓存
        add_header Cache-Control 'no-cache, no-store, must-revalidate, max-age=0';
        add_header Pragma 'no-cache';
        expires off;
    }
}
```

-   `/etc/nginx/sites-available/chat.domain.com.conf`

```nginx
server {
    listen 443 ssl;
    server_name chat.domain.com;

    location / {
        # 支持流式输出
        proxy_buffering off;
        chunked_transfer_encoding on;
        # 升级 websocket
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_pass http://localhost:8081;
    }
}
```

-   `/etc/nginx/sites-available/free.domain.com.conf`

```nginx
server {
    listen 443 ssl;
    server_name free.domain.com;

    location / {
        # 去掉 Host 头部，可以反代到国内服务器
        proxy_set_header Host "";
        proxy_buffering off;
        chunked_transfer_encoding on;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_pass http://localhost:8081;
    }
}
```

### sites-enabled

```shell
sudo ln -s /etc/nginx/sites-available/default_ssl.conf /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/cert.domain.com.conf /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/chat.domain.com.conf /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/free.domain.com.conf /etc/nginx/sites-enabled/
```

