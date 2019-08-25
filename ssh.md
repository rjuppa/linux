### ssh server
```
apt install openssh-server
systemctl status ssh

ssh radekj@192.168.0.105

# ssh-agent can remenber passphrase
ssh-agent -s
ssh-add

service ssh status
systemctl [start/restart/stop/disable/enable] ssh

apt install -y fail2ban
service fail2ban start
```

### on client:
```
# check your pub.key
ls -l ~/.ssh/id_*.pub

# generate cert.
ssh-keygen -t rsa -b 4096 -C "radekj@192.168.0.105"

# copy key to server
ssh-copy-id radekj@192.168.0.105

# ssh-agent can remenber passphrase
ssh-agent -s
ssh-add

# Disabling SSH Password
/etc/ssh/sshd_config
PasswordAuthentication no
PermitRootLogin no

/etc/init.d/ssh reload

# basic security
ufw reset
ufw allow from 147.228.0.0/16 to any port 22
ufw default deny
ufw enable


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
