# Apache Virtual Host (Centos)

1. update & install httpd
2. start / restart httpd & check status
3. open ports and check status
4. setup website files & assign permissions
5. setup virtual host configs
6. disable default site
7. reload httpd

- update & install httpd
```
$ yum update -y
$ yum install -y httpd
```
- start/restart httpd & check status
```
$ systemctl status httpd.service
$ systemctl enable httpd.service --now
$ systemctl restart httpd.service
```
- open ports check status and save config (vultr server)
```
$ yum remove -y firewalld
$ yum install -y iptables-service
$ systemctl start iptables
$ systemctl enable iptables
$ nano /etc/sysconfig/iptables
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
$ systemctl reload iptables
$ iptables -L -n
$ /usr/libexec/iptables/iptables.init save
```
- setup website files & assign permissions
```
$ cd /var/www/html/
$ mkdir -p centos ubuntu
$ echo centos > centos/index.html
$ echo ubuntu > ubuntu/index.html
$ chmod -R 755 /var/www/html
``` 
- setup virtual host configs
```
$ cd /etc/httpd/conf.d/
$ touch centos.conf
<VirtualHost *:80>
    ServerName centos.boomertech.dev
    ServerAlias centos
    DocumentRoot /var/www/html/centos/
</VirtualHost>
$ sed 's/centos/ubuntu/ centos.conf > ubuntu.conf'
```
- disable default site (comment out welcome.conf)
- reload httpd
```
$ systemctl restart httpd.service
```

___


# Apache Virtual Host (Debian)

1. update & install apache2 
2. start/restart apache2 & check status
3. open ports & check status
4. setup website files & assign permissions
5. setup virtual host configs
6. enable virtual host files & disable default site
7. reload apache2

- update & install apache2 
```
$ apt update -y
$ apt install -y apache2
```
- start/restart apache2 & check status
```
$ systemctl status apache2
$ systemctl start apache2
$ systemctl restart apache2
```
- open ports and check status
```
$ ufw allow 80/tcp
$ ufw allow 443/tcp
$ ufw status
$ netstat -pnltu
```
- setup website files & assign permissions
```
$ cd /var/www/html/
$ mkdir -p apache1 apache2
$ echo apache1 > apache1/index.html
$ echo apache2 > apache2/index.html
$ chmod -R 755 /var/www/html
```
- setup virtual host configs
```
$ cd /etc/apache2/sites-available/
$ touch apache1.conf
<VirtualHost *:80>
    ServerName apache1.boomertech.dev
    ServerAlias apache1
    DocumentRoot /var/www/html/apache1/
</VirtualHost>
$ sed 's/apache1/apache2/ apache1.conf > apache2.conf'
```
- enable virtual host files & disable default site
```
$ a2ensite apache1.conf
$ a2ensite apache2.conf
$ a2dissite 000-default.conf
```
- reload apache2
```
$ systemctl restart apache2
```