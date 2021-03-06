- 2 ssh sessiony
- instalace
- iptables
- dns
- disky
- sdileni
- mail/webserver


```
update-alternatives --config editor

apt -y install curl wget apt-transport-https dirmngr

cat /etc/apt/sources.list 
#------------------------------------------------------------------------------#
#                   OFFICIAL DEBIAN REPOS                    
#------------------------------------------------------------------------------#

###### Debian Main Repos
deb http://deb.debian.org/debian/ oldstable main contrib non-free
deb-src http://deb.debian.org/debian/ oldstable main contrib non-free

deb http://deb.debian.org/debian/ oldstable-updates main contrib non-free
deb-src http://deb.debian.org/debian/ oldstable-updates main contrib non-free

deb http://deb.debian.org/debian-security oldstable/updates main
deb-src http://deb.debian.org/debian-security oldstable/updates main

deb http://ftp.debian.org/debian jessie-backports main
deb-src http://ftp.debian.org/debian jessie-backports main


apt -y install vim mc iptables curl cron

apt -y install mdadm
apt -y install lvm2  
apt -y install nfs-common nfs-server nfs-kernel-server
apt -y install samba smbclient cifs-utils

apt -y install bind9 dnsutils
apt -y install ufw fail2ban

apt -y install postfix
apt -y install dovecot-pop3d
apt -y install dovecot-imapd
apt -y install libssl-dev
apt -y install mutt

apt -y install mysql-server
apt -y install postgresql postgresql-contrib
apt -y install php-pgsql 
apt -y install apache2
apt -y install php libapache2-mod-php php-mcrypt php-mysql
apt -y install nginx
apt -y install pound


/etc/exports
/etc/samba/smb.conf

/etc/bind/named.conf.local 
/etc/bind/db.test.spos
/etc/bind/db.192.168.0

/etc/apache2/sites-available/test.spos.conf
/etc/apache2/sites-available/test.spos.ssl.conf
/var/www/test.spos/index.html
/var/www/test.spos/index.php
/etc/nginx/sites-available/test.spos
/etc/nginx/sites-available/test.spos.ssl
/etc/postgresql/10/main/pg_hba.conf

/etc/fstab
/proc/partitions
/proc/mdstat

/etc/postfix/main.cf
/etc/aliases
/etc/dovecot/dovecot.conf
/etc/dovecot/conf.d/10-mail.conf
/etc/dovecot/conf.d/10-ssl.conf


ufw status
iptables -L
df -h
lsblk
dig -t A test.spos @127.0.0.1
ls -l ~/.ssh/id_*

tail /var/log/syslog -n 20


ls -l ~/.ssh/id_*
ssh-keygen -t rsa -b 4096 -C "radekj@eryx.zcu.cz"
ssh-copy-id root@147.228.67.XX

vim /etc/ssh/sshd_config
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no

systemctl restart ssh


# zabezpeceni systemu
iptables -F 	# delete all rules
iptables -L
iptables -A INPUT -p tcp -s 147.228.0.0/16 --dport 22 -j ACCEPT
iptables -A INPUT -j DROP            (zakáže vše ostatní)
iptables-save > /etc/network/iptables.conf
ufw enable


SAMBA =====================
mkdir -p /mnt/samba
chgrp sambashare /mnt/samba

vim /etc/samba/smb.conf
[public]
path = /mnt/samba
browsable = yes
writable = yes
read only = no
force user = nobody

service smbd restart
mount -t cifs //localhost/public /mnt/samba
mount -t cifs //host_name/VIEWER_SHARE -o username=username,password=password /local_path

vim /etc/fstab	# add line
//localhost/public /mnt/samba      cifs   defaults 0 0
netstat -tulpn | grep :138

NFS =====================
netstat -tulpn | grep :111
mkdir /var/nfs/public -p
mkdir /mnt/share -p
chown nobody:nogroup /var/nfs/public

addgroup --gid 6000 nfs_group
adduser --gid 6000 --uid 6000 nfs_share
/etc/exports:
/var/nfs/public 192.168.0.0/24(rw,all_squash,nfs_shareuid=6000,nfs_groupgid=6000)

systemctl restart nfs-kernel-server

# /etc/fstab
192.168.0.106:/var/nfs/public	/mnt/share	nfs	defaults	0	0

showmount -e 192.168.0.106

DNS ==========================
dig -t A test.spos @127.0.0.1
vim /etc/bind/named.conf.local 
vim /etc/bind/db.test.spos
service bind9 restart



LVM ====================
pvcreate /dev/vdd
pvdisplay /dev/vdd
vgcreate vg_data /dev/vdd
lvcreate --name lv_share --size 200M vg_data
lvdisplay /dev/vg_data/lv_share
mkfs.ext4 /dev/vg_data/lv_share
mount /dev/vg_data/lv_share /mnt
lvscan
blkid
mkdir -p /mnt/example
# /etc/fstab
UUID=fcde9bb7-4311-41e2-986a-647a672ebf83       /mnt/example    ext4    defaults        0       2


MDADM RAID =================
cat /proc/mdstat
mdadm --detail /dev/md127
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/loop18 /dev/loop19
mdadm --create /dev/md1 -l5  -n5 /dev/sda1 /dev/sdb1 /dev/sdc1	/dev/sdd1 /dev/sde1
mdadm --create /dev/md1 -l5  -n3 /dev/sda1 /dev/sdb1 missing
mdadm --remove /dev/md0 /dev/sda1
# /etc/fstab
LABEL=seagate_2tb_usb     /media/usb    ext3   defaults 0 0


SSL CERT =================
openssl genrsa -des3 -out server.key 2048
openssl rsa -in server.key -out server.key.insecure
mv server.key server.key.secure
mv server.key.insecure server.key
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
cp server.crt /etc/ssl
cp server.key /etc/ssl

use:
ssl_cert_file = /etc/ssl/server.crt 
ssl_key_file = /etc/ssl//server.key


```
