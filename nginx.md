# Nginx Server Blocks (Centos)

1. update & install nginx
2. enable start stop check status
3. open ports
4. setup website files and assign permissions
5. configure server blocks
6. enable server blocks disable default and restart

- update & install nginx
```
$ yum update -y
$ rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
$ yum install -y nginx
```
- enable start stop check status
```
$ systemctl enable nginx
$ systemctl status nginx
$ systemctl restart nginx
$ systemctl stop nginx
$ systemctl start nginx
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
$ /usr/
```
- setup website files & assign permissions
```
$ cd /var/www/html/
$ mkdir -p nginx1centos nginx2centos
$ echo nginx1centos > nginx1centos/index.html
$ echo nginx2centos > nginx2centos/index.html
$ chmod -R 755 /var/www/html
```
- configure server blocks
```
$ mkdir -p /etc/nginx/sites-available
$ mkdir -p /etc/nginx/sites-enabled
$ nano /etc/nginx/nginx.conf
http {
	include /etc/nginx/sites-enabled/*.conf;
	server_names_hash_bucket_size 64;	
}
$ nginx -t
$ cd /etc/nginx/sites-available
$ touch nginx1centos.conf
server {
    listen       80;
    server_name  nginx1centos.boomertech.dev;
    location / {
        root   /var/www/html/nginx1centos;
        try_files $uri $uri/ =404;
    }
}
$ touch nginx2centos.conf
server {
    listen       80;
    server_name  nginx2centos.boomertech.dev;
    location / {
        root   /var/www/html/nginx2centos;
        try_files $uri $uri/ =404;
    }
}
```
- enable server blocks and disable default
```
$ ln -s /etc/nginx/sites-available/nginx1centos /etc/nginx/sites-enabled/
$ ln -s /etc/nginx/sites-available/nginx2centos /etc/nginx/sites-enabled/
$ cd /etc/nginx/sites-enabled/
$ rm default
```
- verify and restart
```
$ nginx -t
$ systemctl restart nginx
```


___



# Nginx Server Blocks (Debian)

1. update & install nginx
2. enable start stop check status
3. open ports

- update & install nginx
```
$ apt update -y
$ apt install -y nginx
```
- start stop check status
```
$ systemctl status nginx
$ systemctl restart nginx
$ systemctl stop nginx
$ systemctl start nginx
```
- open ports
```
$ ufw allow 'Nginx HTTP'
$ ufw allow 'Nginx HTTPS'
$ ufw reload
$ ufw status
```
- setup website files & assign permissions
```
$ cd /var/www/html/
$ mkdir -p nginx1 nginx2
$ echo nginx1 > nginx1/index.html
$ echo nginx2 > nginx2/index.html
$ chmod -R 755 /var/www/html
```
- configure server blocks
```
$ cd /etc/nginx/sites-available
$ touch nginx1
server {
        listen 80 default_server;
        server_name nginx1.boomertech.dev;
        root /var/www/html/nginx1;
        location / {
                try_files $uri $uri/ =404;
        }
}
$ touch nginx2
server {
        listen 80;
        server_name nginx2.boomertech.dev;
        root /var/www/html/nginx2;
        location / {
                try_files $uri $uri/ =404;
        }
}
```
- enable server blocks and disable default
```
$ ln -s /etc/nginx/sites-available/nginx1 /etc/nginx/sites-enabled/
$ ln -s /etc/nginx/sites-available/nginx2 /etc/nginx/sites-enabled/
$ cd /etc/nginx/sites-enabled/
$ rm default
```
- verify and restart
```
$ nginx -t
$ systemctl restart nginx
```

