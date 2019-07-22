### Apache
```
posts: 80, 443               // netstat -tulpn | grep :8080        <- Who listen on :8080
apt -y install apache2
apt -y install curl

service apache2 restart
#a2enmod ssl
a2enmod | a2dismod
a2ensite | a2dissite

user: www-data / httpd

/etc/apache2/sites-available
/etc/apache2/sites-enabled
/etc/apache2/mods-*
/etc/apache/ports.conf
/var/www/
/var/log/apache2/*access.log

apachectl -t | apachectl -S

cd /etc/apache2/sites-available
cp default-ssl.conf test.spos.conf
mkdir -p /var/www/test.spos/
chmod -R 750 /var/www/test.spos/
chown -R radekj /var/www/test.spos/
chgrp -R www-data /var/www/test.spos/
a2enmod test.spos
service apache2 reload

Listen 8080
<VirtualHost *:8080>
    ServerName test.spos
    ServerAlias test.spos
    DocumentRoot "/var/www/test.spos/"
    #Redirect / https://test.spos/
</VirtualHost>

<VirtualHost *:443>
    ServerName test.spos
    DocumentRoot "/var/www/secure/"

    #SSLEngine on
    #SSLCertificateFile /etc/ssl/server.crt
    #SSLCertificateKeyFile /etc/ssl/server.key

    ErrorLog /var/log/apache2/secure-error.log
    CustomLog /var/log/apache2/secure-access.log common
</VirtualHost>

openssl genrsa -des3 -out server.key 1024
openssl req -new -key server.key -out server.csr
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
cp server.crt /etc/ssl
cp server.key /etc/ssl

curl -k https://www1.test.spos
Vytvoření umělého provozu pomocí nástroje curl (./test http://spos-name.spos 0.001):
#!/bin/bash
get_page () {
        echo "request "$2""
        curl -k "$1" &>/dev/null
}
i=0
while true
do
        ((i=i+1))
        get_page $1 $i
        sleep $2
done
```


### PHP
```
apt -y install ca-certificates apt-transport-https 
wget -q https://packages.sury.org/php/apt.gpg -O- | apt-key add -
echo "deb https://packages.sury.org/php/ jessie main" | tee /etc/apt/sources.list.d/php.list
apt update
apt -y install php php-mysql libapache2-mod-php

vim /etc/php/7.2/apache2/php.ini
cat /etc/php/7.2/cli/php.ini

upload_max_filesize = 16M
memory_limit = 128M
max_input_time = 60
post_max_size = 16M
max_execution_time = 30

open_basedir = ...
disable_functions = ...
disable_classes = ...
allow_url_fopen = On/Off
allow_url_include = On/Off
display_errors = On/Off
error_reporting = E_ALL
log_errors = On/Off

php_admin_flag safe_mode On
php_admin_value open_basedir /var/www/www.test.spos
php_admin_value upload_tmp_dir /var/www/www.test.spos/phptmp
php_admin_value session.save_path /var/www/www.test.spos/phptmp

<?php
  phpinfo();
?>

<?php
    $page = $_GET['page'];
    $xfile = fopen($page, "r") or die("Unable to open file!");
    echo fgets($xfile);
    fclose($xfile);
?>

```


### NGINX
```
apt -y install nginx      - nesmí nic poslouchat na portu 80

vim /etc/nginx/sites-available/test.spos
server {
        listen   80; 
        root /var/www/test.spos/; 
        index index.php index.html index.htm;

	//listen   443; 
	//ssl    on;
	//ssl_certificate    /etc/ssl/server.crt;     // (.pem or .crt)
	//ssl_certificate_key    /etc/ssl/server.key;
	
        server_name test.spos; 
	access_log /var/log/nginx/test.spos.access.log;
	error_log /var/log/nginx/test.spos.error.log;
	
	// MVC entry point
        //location / {
        //  try_files $uri $uri/ /index.php;
        //}

        location ~ \.php$ {
          proxy_set_header X-Real-IP  $remote_addr;
          proxy_set_header X-Forwarded-For $remote_addr;
          proxy_set_header Host $host;
          proxy_pass http://127.0.0.1:8080;
         }
         location ~ /\.ht {
                deny all;
        }
}

rm /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/test.spos /etc/nginx/sites-enabled/test.spos
systemctl restart nginx

vim /etc/apache2/ports.conf		// Apache listen on 127.0.0.1:8080
NameVirtualHost 127.0.0.1:8080
Listen 127.0.0.1:8080

# set apache
vim /etc/apache2/sites-available/test.spos
change <VirtualHost 127.0.0.1:8080>
service apache2 restart

# load balancing
nginx.conf
events {
  worker_connections  1024; 
}

http {
  upstream backend {
    server 127.0.0.1:8000 weight=3;
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
  }

  server {
    listen 80;
    server_name test.spos;
    location / {
      proxy_pass http://backend;
    }
  }
  
    server {
    listen 443 ssl;
    root /var/www/web1/;
        ssl    on;
        ssl_certificate    /etc/ssl/certs/ssl-cert-snakeoil.pem;
        ssl_certificate_key    /etc/ssl/private/ssl-cert-snakeoil.key;

    server_name test.spos;
    location / {
      proxy_pass http://backend;
    }
  }
}


# DNS round robin
www	IN	A	147.228.67.42
www	IN	A	147.228.67.43
www	IN	A	147.228.67.44
```

### Pound
```
apt -y install pound
/etc/pound/pound.cfg
	Service
		HeadRequire "Host:.*www.test.spos"
		BackEnd
			Address	127.0.0.1
			Port	80
			Priority 5
		End
		BackEnd
			Address 10.228.67.42
			Port    80
			Priority 5
		End
	End

poundctl -c /var/run/pound/poundctl.socket
poundctl -c /var/run/pound/poundctl.socket -b 0 0 1

```
