### NFS
```
apt install nfs-common nfs-server nfs-kernel-server
vim /etc/fstab
ports: 111, 2049 both
netstat -tulpn | grep :111
showmount -e 192.168.0.106

# autentikace mezi systemy se prenasi podle uid/gid
addgroup --gid 6000 nfs_group
adduser --gid 6000 --uid 6000 nfs_share

/etc/exports:
/mnt/raid5 192.168.0.0/24(rw,all_squash,anonuid=6000,anongid=6000)

usermod -a -G user1 grp1
gpasswd -d user1 grp1

chown nobody:nogroup /mnt/raid5
chmod -R 770 /mnt/raid5/
lsblk
mount /dev/md127 /mnt/raid5
/etc/init.d/nfs-kernel-server start
exportfs -r

# client: 
addgroup --gid 6000 nfs_share
adduser --gid 6000 --uid 6000 nfs_share
mount 192.168.0.106:/mnt/raid5 /mnt/raid5

# /etc/fstab
192.168.0.106:/mnt/raid5	/mnt/raid5	nfs	defaults	0	0

showmount -e 192.168.0.106

mount -t nfs /dev/vdb1 /mnt
mount /mnt/nfs
fuser -m DIRECTORY # kdo tam ma neco otevreneho
```

### SAMBA
```
apt install samba smbclient cifs-utils
vim /etc/samba/smb.conf
service smbd restart

TCP ports 139 and 445
UDP ports 137 and 138
netstat -tulpn | grep :139

addgroup --gid 6001 cifs
usermod -G cifs radekj

mkdir /samba
chgrp sambashare /samba
mkdir /samba/radek_smb

useradd -M -d /samba/radek_smb -s /usr/sbin/nologin -G sambashare radek_smb
chown radek_smb:sambashare /samba/radek_smb
chmod 2770 /samba/radek_smb
smbpasswd -a radek_smb
smbpasswd -e radek_smb

useradd -M -d /samba/users -s /usr/sbin/nologin -G sambashare sadmin
mkdir /samba/users
chown sadmin:sambashare /samba/users
smbpasswd -a sadmin
smbpasswd -e sadmin
chmod 2770 /samba/users
pdbedit -w -L

[users]
    path = /samba/users
    browseable = yes
    read only = no
    force create mode = 0660
    force directory mode = 2770
    valid users = @sambashare @sadmin
[radek_smb]
    path = /samba/radek_smb
    browseable = no
    read only = no
    force create mode = 0660
    force directory mode = 2770
    valid users = radek_smb @sadmin

smbclient //localhost/share1 -U radekj
smbclient -L localhost

mount -t cifs //localhost/share1 /mnt/share -o username=radekj
vim /etc/fstab	# add line
//server/docs /mnt/samba      smbfs   username=admin,password=pass 0 0

mount a   # namontuj fstab
```
