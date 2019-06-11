### Apache
```
posts: 80, 443
apt-get install apache2
apt-get install curl

service apache2 reload/status/restart
#a2enmod ssl
a2enmod | a2dismod
a2ensite | a2dissite

user: www-data / httpd

/etc/apache2/sites-*
/etc/apache2/mods-*
/etc/apache/ports.conf
/var/www/
/var/log/apache2/*access.log
/var/log/apache2/*error.log

apachectl -t | apachectl -S

cd /etc/apache2/sites-available
cp default-ssl.conf test.spos.conf
mkdir -p /var/www/test.spos/
chmod -R 750 /var/www/test.spos/
chown -R radekj /var/www/test.spos/
chgrp -R www-data /var/www/test.spos/
a2enmod test.spos
service apache2 reload

<VirtualHost *:80>
    ServerName test.spos
    ServerAlias test.spos
    DocumentRoot "/var/www/test.spos/"
    #Redirect / https://test.spos/
</VirtualHost>

<VirtualHost *:443>
    ServerName test.spos
    DocumentRoot "/var/www/secure/"

    #SSLEngine on
    #SSLCertificateFile /etc/apache2/ssl/server.crt
    #SSLCertificateKeyFile /etc/apache2/ssl/server.key

    ErrorLog /var/log/apache2/secure-error.log
    CustomLog /var/log/apache2/secure-access.log common
</VirtualHost>

openssl genrsa -des3 -out server.key 1024
openssl req -new -key server.key -out server.csr
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

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
sudo apt install php libapache2-mod-php php-mysql

/etc/php5/apache2
/etc/php5/cli

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
apt-get install nginx - nesmí nic poslouchat na portu 80

sudo vim /etc/nginx/sites-available/radekj.spos
server {
        listen 80;
        listen [::]:80;

        root /var/www/radekj.spos/;
        index index.html index.htm

        server_name radekj.spos www.radekj.spos;

        location / {
                try_files $uri $uri/ =404;
        }
}

sudo systemctl restart nginx

/etc/nginx/sites-available/www.conf - vytvořit
	upstream www1.test.spos { #tady je jmeno stranky, kterou chceme balancovat
		ip_hash; #rika, ze klient bude vzdy komunikovat se stejnym server jako minule (nemusi tam byt)
	        server  192.168.0.104:8008      weight=3        max_fails=2     fail_timeout=10s; #adresy s portem serveru, mezi kterymi se ma balancovat
	        server  192.168.0.105:8080      weight=1        max_fails=2     fail_timeout=10s;
	}
	server {
 	       listen  80; #port, na kterem ma nginx poslouchat
 	       server_name www1.test.spos; #jmeno stranky, kterou chceme balancovat
		
		location /static {
			root /var/www/web01; #adresar static bude brat ze zadaneho adresare
		}

 	       location / {
 	               proxy_pass http://www1.test.spos; #jmeno stranky, kterou chceme balancovat
	       }
 	}
	#weigh - server s wight=3 bude prebirat 3x vice trafficu nez server s weight=1
	#max_fails - kolikrat muze selhat pripojeni k danemu serveru nez ho ngnix prohlasi za offline
	#fail_timeout - udava, po jak dlouhych intervalech se bude nginx snazit pripojit k serveru, ktery je down
ln -s /etc/nginx/sites-available/www.conf /etc/nginx/sites-enabled/ - přidá stránku do sites-enabled

```
