### NFS
```
sudo apt install nfs-common, nfs-server, nfs-kernel-server
sudo vim /etc/fstab
ports: 111, 2049 both

# autentikace mezi systemy se prenasi podle uid/gid
addgroup --gid 6000 nfs_group
adduser --gid 6000 --uid 6000 nfs_share

/etc/exports:
/mnt/raid5 192.168.0.0/24(rw,all_squash,nfs_shareuid=6000,nfs_groupgid=6000)

usermod -a -G user1 grp1
gpasswd -d user1 grp1

hown nobody:nogroup /mnt/raid5
chmod -R 770 /mnt/raid5/
lsblk
sudo mount /dev/md127 /mnt/raid5
/etc/init.d/nfs-kernel-server start
exportfs -r

showmount -e 192.168.0.106

# client: 
adduser --gid 6000 --uid 6000 nfs_share
sudo mount 192.168.0.106:/mnt/raid5 /mnt/raid5

# /etc/fstab
192.168.0.106:/mnt/raid5	/mnt/raid5	nfs	defaults	0	0

sudo mount -t ntfs /dev/vdb1 /mnt
sudo mount /mnt/nfs
fuser -m DIRECTORY # kdo tam ma neco otevreneho
```

### SAMBA
```
sudo apt install samba smbclient cifs-utils
sudo vim /etc/samba/smb.conf
sudo systemctl status/restart nmbd

TCP ports 139 and 445
UDP ports 137 and 138

addgroup --gid 6001 cifs
usermod -G cifs radekj

sudo mkdir /samba
sudo chgrp sambashare /samba
sudo mkdir /samba/radek_smb

sudo useradd -M -d /samba/radek_smb -s /usr/sbin/nologin -G sambashare radek_smb
sudo chown radek_smb:sambashare /samba/radek_smb
sudo chmod 2770 /samba/radek_smb
sudo smbpasswd -a radek_smb
sudo smbpasswd -e radek_smb

sudo useradd -M -d /samba/users -s /usr/sbin/nologin -G sambashare sadmin
sudo mkdir /samba/users
sudo chown sadmin:sambashare /samba/users
sudo smbpasswd -a sadmin
sudo smbpasswd -e sadmin
sudo chmod 2770 /samba/users
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