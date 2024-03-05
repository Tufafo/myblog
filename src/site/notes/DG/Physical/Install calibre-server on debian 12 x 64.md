---
{"dg-publish":true,"permalink":"/dg/physical/install-calibre-server-on-debian-12-x-64/"}
---


## Introduce

Calibre-server is really a convenient reading server online.

Recently, my previous server has expired. So I reinstall the calibre-server on my new linux server. Now record the configuration steps. My server system is Debian 12 x 64.

## The install of calibre

Install the dependences. Expect what I can see in the refference, I also need the `libxcb-cursor0` .

```bash
sudo apt update && sudo apt install -y libfontconfig libgl1-mesa-glx libxcb-cursor0
```

Then install calibre-server from the official site.

```bash
wget https://download.calibre-ebook.com/linux-installer.sh

sh linux-installer.sh
```

The calibre will be install in the directory of `/opt/calibre` .

## Start calibre-server

If you haven't create any calibre e-book library previously, you can create a new library with `calibre-db` command.

```bash
calibredb create_database [path to booklib]
```

But now, my task is to recover my former library.

The calibre-server provide network service from the port 8080. Now we need to open this port to allow the passing of traffic packets.

```bash
ufw allow 8080
```

Now start calibre-server from my previous booklib.

```bash
calibre-server [booklib name]
```

Access the server from browser with url `http://ip:8080` or `http://domain:8080` to verificate if the calibre-server have started correctly.

## Configure the system service

If you can access the booklib from browser correctly, the create a file `/etc/systemd/system/calibre` , and input these contents:

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

Do not forget to replace the `[user-name]` , `[group-name]` , `[booklib-names]` with your own name. If you have multiple booklibs, split them with spaces.

Save the file, enable and start the system service.

```bash
systemctl enable calibre-server
systemctl start calibre-server
```

## Enable user authentication

Calibre-server support user authentication. Add `--enable-auth` after the start command in the `calibre-server.service` file.

```
ExecStart=/opt/calibre/calibre-server [booklib-names] --enable-auth
```

Now, add a new user.

```bash
calibre-server --manage-users
```

Input your user name and password according the tips.

Restart the calibre-server.

```bash
systemctl restart calibre-server
```

Browse the server to make sure the calibre-server need user authentication.

## Configuration of SSL

### Enable apache2

First we need install apache2 to enable the http service.

```bash
apt install apache2
```

Start the proxy mod.

```bash
a2enmod proxy proxy_http
```

Add the network configuration file `/etc/apache2/sites-avaliable/[domain].conf` , and input these contents:

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

Http service use the port 80 by default. Reset the firewall.

```bash
ufw delete allow 8080
ufw allow 80
```

Make a authentication by assess `http://[domain]` to see if you can access your calibre-server.

### Add SSL

Now add SSL service by certbot.

There is a little different between Ubuntu and Debian. You need to install certbot dependence `python3-certbot-apache`.

```bash
apt install python3-certbot-apache
apt install certbot
```

Use `certbot` command to apply for a SSL certificate.

```bash
certbot apache
```

Input a valid email adress, agree to register your email in the ACME server, choose if to share your email adress with Electronic Frontier Foundation, your domain one by one.

After the apply, your SSL certificate file will be saved in the directory `/etc/letsencrypy/live/[domain]` . The certificate file name is: `fullchain.pem` , and the key file name is `privkey.pem` .

The default valid date is three month.

SSL service the interaction between the server and the client with port 443. So open the firewall of port 443.

```bash
ufw allow 443
```

In the end, access the url `https://[domain]` to enjoy your reading time.

Thanks for your read. I will record more program experience in the future.

## References

[How To Create a calibre Ebook Server on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-create-a-calibre-ebook-server-on-ubuntu-20-04)

[a2ensite (8) - Linux Manuals](https://www.systutorials.com/docs/linux/man/8-a2ensite/)

[github: "The requested apache plugin does not appear to be installed" on raspbian](https://github.com/certbot/certbot/issues/3854)