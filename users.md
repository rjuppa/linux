
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

chmod 600 ~/.ssh/authorized_keys


ls -la /home/username/
drwxr-xr-x 2 username username 4096 Dec 11 11:23 .
drwxr-xr-x 4 root     root     4096 Dec 11 11:23 ..
-rw-r--r-- 1 username username  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 username username 3771 Apr  4  2018 .bashrc
-rw-r--r-- 1 username username  807 Apr  4  2018 .profile

chmod +x /path/to/your/file.txt
chmod g=u script.sh
chmod 750 script.sh
chmod -R a+X logs_dir

mkdir -p 777 /var/www/dir/dir2

rm -rf path/to/directory/*    (delete all files in derectory recursively)
sudo ln -s /Users/devroot/statusdroid/src /app

gzip -k file.txt
gzip -r *
gzip -d file.txt.gz

printenv | grep ARTI

find / -name "aaa.txt"
find . -name "*.bak" -type f -delete
grep -rnw /etc/apt/ -e "launchpad"

whereis firefox  (for programs)
which firefox

ps -A 
top -h
killall firefox    (The killall command kills all the processes by name.)
kill -9 <processid>


```
