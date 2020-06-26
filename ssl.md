# SSL on Apache (Centos)

1. install ssl module 
2. get letsencrypt certificate
3. auto renew
4. crontab

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
$ ./letsencrypt-auto --apache -d centos.boomertech.dev 
$ ./letsencrypt-auto --apache -d ubuntu.boomertech.dev
```
- auto renew
```
$ cd /usr/local/letsencrypt
$ ./letsencrypt-auto certonly --apache --renew-by-default -d centos.boomertech.dev 
$ ./letsencrypt-auto certonly --apache --renew-by-default -d ubuntu.boomertech.dev
```
- cronjob to auto renew every 90 days
```
$ cd /usr/local/bin
$ touch le-renew-centos
#!/bin/bash

domain=$1
le_path='/usr/local/letsencrypt'
le_conf='/etc/letsencrypt'
exp_limit=30;

get_domain_list(){
        certdomain=$1
        config_file="$le_conf/renewal/$certdomain.conf"

        if [ ! -f $config_file ] ; then
                echo "[ERROR] The config file for the certificate $certdomain was not found."
                exit 1;
        fi

        domains=$(grep --only-matching --perl-regex "(?<=domains \= ).*" "${config_file}")
        last_char=$(echo "${domains}" | awk '{print substr($0,length,1)}')

        if [ "${last_char}" = "," ]; then
                domains=$(echo "${domains}" |awk '{print substr($0, 1, length-1)}')
        fi

        echo $domains;
}

if [ -z "$domain" ] ; then
        echo "[ERROR] you must provide the domain name for the certificate renewal."
        exit 1;
fi

cert_file="/etc/letsencrypt/live/$domain/fullchain.pem"

if [ ! -f $cert_file ]; then
        echo "[ERROR] certificate file not found for domain $domain."
        exit 1;
fi

exp=$(date -d "`openssl x509 -in $cert_file -text -noout|grep "Not After"|cut -c 25-`" +%s)
datenow=$(date -d "now" +%s)
days_exp=$(echo \( $exp - $datenow \) / 86400 |bc)

echo "Checking expiration date for $domain..."

if [ "$days_exp" -gt "$exp_limit" ] ; then
        echo "The certificate is up to date, no need for renewal ($days_exp days left)."
        exit 0;
else
        echo "The certificate for $domain is about to expire soon. Starting renewal request..."
        domain_list=$( get_domain_list $domain )
        "$le_path"/letsencrypt-auto certonly --apache --renew-by-default --domains "${domain_list}"
        echo "Restarting Apache..."
        /usr/bin/systemctl restart httpd
        echo "Renewal process finished for domain $domain"
        exit 0;
fi
```
- grant execution install bc and run to test
```
$ yum install bc
$ chmod +x /usr/local/bin/le-renew-centos
$ /usr/local/bin/le-renew-centos centos.boomertech.dev
$ /usr/local/bin/le-renew-centos ubuntu.boomertech.dev
```
- add a crontab to run every 2 months
```
$ crontab -e
0 1 1 */2 * /usr/local/bin/le-renew-centos centos.boomertech.dev >> /var/log/centos.boomertech.dev-renew.log 2>&1
0 1 1 */2 * /usr/local/bin/le-renew-centos ubuntu.boomertech.dev >> /var/log/ubuntu.boomertech.dev-renew.log 2>&1
```

___


# SSL on Nginx (Centos)

1. install ssl module
2. get letsencrypt certificate
3. auto renew
4. crontab

- install ssl module
```
$ yum install -y epel-release
$ yum install -y certbot-nginx
```
- get letsencrypt certificate
```
$ certbot --nginx -d nginx1centos.boomertech.dev -d nginx2centos.boomertech.dev
$ nginx -t
$ systemctl reload nginx
```
- auto renew
```
$ crontab -e
15 3 * * * /usr/bin/certbot renew --quiet

```
___


# SSL on Apache (Debian)

1. install ssl module 
2. get letsencrypt certificate
3. auto renew
4. crontab

- install ssl module & let's encrypt
```
$ a2enmod ssl
$ a2ensite default-ssl.conf
$ systemctl reload apache2
$ systemctl restart apache2
$ cd /usr/local
$ sudo git clone https://github.com/letsencrypt/letsencrypt
```
- get a letsencrypt certificate
```
$ cd /usr/local/letsencrypt
$ ./letsencrypt-auto --apache -d apache1.boomertech.dev
$ ./letsencrypt-auto --apache -d apache2.boomertech.dev
$ ls /etc/letsencrypt/live
```
- auto renew
```
$ cd /usr/local/letsencrypt
$ ./letsencrypt-auto certonly --apache --renew-by-default -d apache1.boomertech.dev 
$ ./letsencrypt-auto certonly --apache --renew-by-default -d apache2.boomertech.dev
```
- cronjob to auto renew every 90 days
```
$ crontab -e

0 1 1 */2 * cd /usr/local/letsencrypt && ./letsencrypt-auto certonly --apache --renew-by-default --apache -d apache1.boomertech.dev >> /var/log/apache1.boomertech.dev-renew.log 2>&1

0 1 1 */2 * cd /usr/local/letsencrypt && ./letsencrypt-auto certonly --apache --renew-by-default --apache -d apache2.boomertech.dev >> /var/log/apache2.boomertech.dev-renew.log 2>&1

```
- renewal domain config file
```
$ cat /etc/letsencrypt/options-ssl-apache.conf

```
- updated apache2 files
```
$ ls /etc/apache2/sites-enabled/
$ cat /etc/apache2/sites-enabled/apache1-le-ssl.conf
```


___


# SSL on Nginx (Debian)



