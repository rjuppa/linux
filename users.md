
```
cat /etc/passwd
cat /etc/group
grep radekj /etc/passwd

useradd -m -s /bin/bash radekj    (-m = create home)
useradd -u 6000 radekj

groupadd groupname
groupdel groupname
sudo useradd -g users -G wheel,developers,mail,sudo radekj
usermod -aG sudo radekj   # add sudouser
gpasswd -d radekj groupname     (remove user from group)
passwd radekj
id radekj

logout
reboot

chmod -R 777 /var/dir
chown -R nobody:nogroup /mnt/raid5
chgrp -R nogroup /var/dir


ls -la /home/username/
drwxr-xr-x 2 username username 4096 Dec 11 11:23 .
drwxr-xr-x 4 root     root     4096 Dec 11 11:23 ..
-rw-r--r-- 1 username username  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 username username 3771 Apr  4  2018 .bashrc
-rw-r--r-- 1 username username  807 Apr  4  2018 .profile




```
