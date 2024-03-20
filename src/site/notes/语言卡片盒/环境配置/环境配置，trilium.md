---
{"dg-publish":true,"permalink":"/语言卡片盒/环境配置/环境配置，trilium/"}
---



Trilium 是一个便捷的笔记软件。本篇博客记录trilium的配置过程。


在bash中下载trilium文件。

```bash
weget https://github.com/zadam/trilium/releases/download/v0.62.6/trilium-linux-x64-server-0.62.6.tar.xz
```

解压文件。

```bash
tar -xvf trilium-linux-x64-server-0.62.6.tar.xz
mv trilium-linux-x64-server /opt/trilium
```

设置系统服务文件 `/etc/systemd/system/trilium-service` 

```bash
[Unit]
Description=Trilium Daemon
After=syslog.target network.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/opt/trilium/trilium.sh
WorkingDirectory=/opt/trilium/

TimeoutStopSec=20
# KillMode=process leads to error, according to https://www.freedesktop.org/software/systemd/man/systemd.kill.html
Restart=always

[Install]
WantedBy=multi-user.target
```

Trilium默认端口为8080。打开浏览器，输入 `ip/域名+8080` ，访问triliun网站，初始化密码。

不过当前端口设置为8080，需要修改 `~/trilium-data/config.ini`  中的端口以及关闭ssl。

```ini
port=2024
https=true
certPath=<证书文件地址>
keyPath=<证书私匙地址>
```

重启trilium

```bash
systemctl restart trilium
```

访问 `ip/域名+2024` 成功。

## 参考

- [Github: zadam/trilium/Packaged server installation](https://github.com/zadam/trilium/wiki/Packaged-server-installation)
