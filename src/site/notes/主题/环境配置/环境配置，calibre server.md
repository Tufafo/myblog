---
{"dg-publish":true,"permalink":"/主题/环境配置/环境配置，calibre server/","tags":["环境配置"]}
---



## 说明

calibre-server是一种非常便利的线上电子阅读服务。
之前的服务器到期了，重新配置了calibre-server。现在顺便整理一下操作步骤。

## calibre 安装

操作系统，debian `12 * 64`

安装依赖项。除了参考中列出的，本环境还缺少libxcb-cursor0：

```bash
sudo apt update && sudo apt install -y libfontconfig libgl1-mesa-glx libxcb-cursor0
```

从官网安装 calibre：

```bash
wget https://download.calibre-ebook.com/linux-installer.sh
```

```bash
sh linux-installer.sh
```

书库安装在/opt/calibre目录下。
## 启动 calibre

如果之前没有创建过书库的话，可以通过 `calibre-db` 命令创建新的电子书库。

```bash
calibredb create_database [path to booklib]
```

不过我现在的任务是恢复之前的书库。

calibre server从8080端口进行网络服务，而vultr默认打开防火墙。现在需要开放服务器端口8080以允许流量通过：

```bash
ufw allow 8080
```


从备份书库启动 calibre-server：

```bash
calibre-server [booklib name]
```

从浏览器访问服务器 `http://ip:8080` `https://domain:8080` 以验证服务运行是否成功。

## 配置系统服务

如果能成功从浏览器访问书库，进入下一步。创建系统配置文件 `/etc/systemd/system/calibre`

输入以下配置：

```
## startup service
[Unit]
Description=calibre content server
After=network.target


[Service]
Type=simple
User=[user-name]
Group=[group-name]
ExecStart=/opt/calibre/calibre-server [booklib-names]

[Install]
WantedBy=multi-user.target
```

其中 `user-name`、`group-name`、`booklib-names` 更换为本系统下用户名，用户组，书库名即可。如果有多个书库，以空格分开。

保存该文件，应用和启动系统服务。

```bash
systemctl enable calibre-server
systemctl start calibre-server
```

 calibre-server 支持用户认证。在系统服务文件的启动命令后增加 `--enable-auth` 配置

```
...
ExecStart=/opt/calibre/calibre-server [booklib-names] --enable-auth
...
```

现在，增加新的用户。

```bash
calibre-server --manage-users
```

根据提示选择增加用户并输入用户名密码即可。

重新启动 `calibre-server` 

```bash
systemctl restart calibre-server
```

浏览服务端查看是否需要用户认证。

## SSL配置

### 应用apache2

认证成功后，现在为 `calibre-server` 增加SSL配置。

首先安装 `apache2` 。

```bash
apt install apache2
```

启动 `apache2` 代理模块。

```bash
a2enmod proxy proxy_http
```

增加网络配置文件 `etc/apache2/sites-available/[domain].conf` ，输入以下内容：

```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so

<VirtualHost *:80>
    ServerName your_domain
    AllowEncodedSlashes O
    ProxyPreserveHost On
    ProxyPass "/"  "http://localhost:8080/"
</VirtualHost>
```


重新设置防火墙。

```bash
ufw delete allow 8080
ufw allow 80
```

删除默认网站并启动calibre网站。

```bash
a2dissite 000-default
a2ensite [domian].conf
systemctl reload apache2
```

通过访问 `http://[domain]` 验证网站是否配置成功。

### 增加SSL

现在利用 `certbot` 增加 SSL 认证。

安装 `certbot` 依赖和 `certbot` 本体。

```bash
apt install python3-certbot-apache
apt install certbot
```

利用 `certbot` 进行apache SSL 证书申请和安装。

```bash
certbot apache
```

依次输入有效的邮件地址，同意注册邮箱到ACME 服务器，选择是否和 Electronic Frontier Foundation 分享邮件地址，输入域名。

注册好后，证书文件保存在 `/etc/letsencrypt/live/[domain]/` 目录下。
证书文件名为：`fullchain.pem` ，密匙文件名为： `privkey.pem` 。

证书默认生效日期是三个月。

SSL默认同过443端口实现用户端和服务器的交互，现在打开防火墙的 443 端口。

```bash
ufw allow 443
```

最后，通过 `https://[domain]` 查看是否可以访问 `calibre-server` 进行书籍阅读。
## 参考

[How To Create a calibre Ebook Server on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-create-a-calibre-ebook-server-on-ubuntu-20-04)

[a2ensite (8) - Linux Manuals](https://www.systutorials.com/docs/linux/man/8-a2ensite/)

[github: "The requested apache plugin does not appear to be installed" on raspbian](https://github.com/certbot/certbot/issues/3854)