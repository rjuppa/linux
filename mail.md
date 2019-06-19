### POSTFIX
```
apt-get install postfix
postfix status
service postfix restart
less /var/log/mail.log

# každý uživatel, kterému má být možno poslat mail, musí být ve skupině mail
useradd -m -s /bin/bash user1
usermod -G mail user1

# Modifikace DNS pro maily (domena test.spos, adresa 192.168.0.106):
mail    IN      A       192.168.0.106
@       IN      MX 10   mail

/etc/postfix/main.cf:
myorigin = test.spos
myhostname = test.spos
mydomain = test.spos
mydestination = $myhostname, posta.test.spos, green, localhost.localdomain, localhost
mynetworks = 192.168.0.0/24 127.0.0.0/8 
home_mailbox = Maildir/
mailbox_command =

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
apt-get install dovecot-pop3d
apt-get install dovecot-imapd

vim /etc/dovecot/dovecot.conf
protocols = imap pop3 imaps pop3s

vim /etc/dovecot/conf.d/10-ssl.conf 
ssl= yes | no | required
ssl_disable = no
ssl_cert_file = /etc/ssl/certs/server.crt 
ssl_key_file = /etc/ssl/private/server.key

/etc/dovecot/conf.d/10-mail.conf
mail_location = mbox:~/mail:INBOX=/var/mail/%u
mail_location = maildir:~/Maildir

useradd -m -s /bin/bash john
mkdir /home/john/Maildir
chown john:john /home/john/Maildir
chmod -R 700 /home/john/Maildir 

/etc/init.d/dovecot start
/etc/init.d/postfix start


Čtení pošty šifrovaně přes IMAP (nebo POP3), mutt:
10-mail.conf - stejně jako pro telnet 
cd /etc/dovecot
mkdir certs
cd certs
openssl genrsa -des3 -out server.key 1024
openssl req -new -key server.key -out server.csr
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
- vytvoří soubory server.crt a server.key (stejné jako pro SSL u webu)

/etc/dovecot/conf.d/10-ssl.conf
	ssl = yes
	ssl_cert = </etc/dovecot/certs/server.crt
	ssl_key = </etc/dovecot/certs/server.key

~/.muttrc (v home adresáři uživatele, ze kterého se přihlašujeme)
	- pro imap
	set folder = imap://user:pass@localhost:143/
	set spoolfile = imap://user:pass@localhost:143/
	- pro pop3
	set folder = pop://user:pass@localhost:110/
	set spoolfile = pop://user:pass@localhost:110/
user - uživatel, na kterého se přihlašujeme
```


### MUTT - prohlížení mailů
```
apt-get install mutt
mutt -f Maildir/
mutt -f /home/user2/Maildir
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
telnet localhost 143

SMTP 25; POP3 110; IMAP 143; IMAPS 993; POP3S 995
```
