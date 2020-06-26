# SSL on Apache (Centos)

1. install ssl module 

- install ssl module & let's encrypt
```
$ yum -y install mod_ssl
$ yum install -y epel-release
$ yum instal -y git
$ cd /usr/local
$ git clone https://github.com/letsencrypt/letsencrypt
```
- get a letsencrypt certificate
```
$ cd /usr/local/letsencrypt
$ ./letsencrypt-auto --apache -d centos.boomertech.dev -d ubuntu.boomertech.dev
```
- auto renew
```
$ cd /usr/local/letsencrypt
$ ./letsencrypt-auto certonly --apache --renew-by-default -d centos.boomertech.dev -d ubuntu.boomertech.dev
```


