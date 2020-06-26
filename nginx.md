# Nginx Server Blocks (on Debian)

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

