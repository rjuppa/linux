### POSTFIX
```
apt-get install postfix
postfix status
service postfix restart
less /var/log/mail.log

# každý uživatel, kterému má být možno poslat mail, musí být ve skupině mail
usermod -G mail user 

# Modifikace DNS pro maily (domena test.spos, adresa 192.168.0.106):
mail    IN      A       192.168.0.106
@       IN      MX 10   mail

/etc/postfix/main.cf:
myorigin = test.spos
myhostname = test.spos
mydomain = test.spos
mydestination = $myhostname, green, localhost.localdomain, localhost
mynetworks = 192.168.0.0/24 127.0.0.0/8 
home_mailbox = Maildir/

service postfix restart
echo "Test2" | mail -s "Testovaci mail2" radekj@test.spos
ls -l /var/mail/

telnet localhost 25
  HELO test.spos
  MAIL FROM: pepa@gmail.xx
  RCPT TO: radekj@test.spos
  DATA
  Subject: Testovaci
  Ahoj svete
  .
  QUIT

cat /var/mail/radekj 
cat /var/spool/mail/radekj 

cat /etc/aliases:
hostmaster:	user1
webmaster:	user2

newaliases
```



### DOVECOT
```
sudo apt-get install dovecot-pop3d
sudo apt-get install dovecot-imapd

sudo useradd -m -s /bin/bash john
mkdir /home/john/Maildir
chown john:john /home/john/Maildir
chmod -R 700 /home/john/Maildir 

/etc/init.d/dovecot start
/etc/init.d/postfix start
```


### MUTT - prohlížení mailů
```
sudo apt-get install mutt
mutt -f Maildir/
sudo mutt -f /home/user2/Maildir
mutt

# pres IMAP
~/.muttrc:
set folder = imap://jmeno:heslo@localhost:143/
set spoolfile = imap://jmeno:heslo@localhost:143/
chmod 777 .muttrc

```

### test
```
telnet localhost 110
openssl s_client -connect localhost:995

SMTP 25; POP3 110; IMAP 143; IMAPS 993; POP3S 995
```
