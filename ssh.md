### ssh server
```
apt install openssh-server
service sshd status

ssh radekj@192.168.0.105

# ssh-agent can remenber passphrase
ssh-agent -s
ssh-add

service sshd [start/restart/stop/disable/enable]

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
# with enabled SSH Password than turn it off
exec ssh-agent $SHELL
ssh-copy-id -i /Users/radekj/.ssh/id_rsa.pub radekj@tarkil.metacentrum.cz

# remove old key from known_hosts
ssh-keygen -R "hostname"

# ssh-agent can remenber passphrase
ssh-agent -s
ssh-add

# Disabling SSH Password
/etc/ssh/sshd_config
PasswordAuthentication no
PermitRootLogin no

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
