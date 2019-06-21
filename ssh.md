### ssh server
```
apt install openssh-server
systemctl status ssh

ssh radekj@192.168.0.105

systemctl start/restart/stop/disable/enable ssh
```

### on client:
```
# check your pub.key
ls -l ~/.ssh/id_*.pub

# generate cert.
ssh-keygen -t rsa -b 4096 -C "radekj@192.168.0.105"

# copy key to server
ssh-copy-id radekj@192.168.0.105

# Disabling SSH Password
/etc/ssh/sshd_config
PasswordAuthentication no
PermitRootLogin no

/etc/init.d/ssh reload

# sudouser
useradd -m -s /bin/bash radekj
passwd radekj
usermod -aG sudo radekj   # add sudouser
su - radekj
id radekj
logout
```

### rsync - backup
```
apt-get install -y openssh-server
rsync -av ~/TEST /data/
rsync -av -e ssh /data backup@192.168.1.228:/backup/

zip /data/MONDAY.zip /data/MONDAY && rsync -av -e ssh /data/MONDAY.zip backup@192.168.1.228:/backup/

```
