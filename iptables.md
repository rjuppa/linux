### iptables
```
apt-get install iptables

iptables -A  -i <interface> -p <(tcp/udp) > -s <source> --dport <port>  -j <target>
targets: ACCEPT, DROP and RETURN
chains: INPUT, FORWARD, OUTPUT

# accept connections
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp -s 147.228.0.0/16 --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# samba
iptables -A INPUT -p udp --dport 137 -j ACCEPT
iptables -A INPUT -p udp --dport 138 -j ACCEPT
iptables -A INPUT -p tcp --dport 139 -j ACCEPT
iptables -A INPUT -p tcp --dport 445 -j ACCEPT

# deny connections
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -s 192.168.1.3 -j ACCEPT
iptables -A INPUT -j DROP		# Dropping all other traffic

iptables -F 	# delete all rules

iptables -L --line-numbers 	# list rules
iptables -D INPUT 3 	# delete line no.3

/sbin/iptables-save 	# save changes

```

### ufw
```
apt install ufw
ufw status verbose
/etc/default/ufw

ufw app list 	# list of profiles
ufw allow OpenSSH
ufw allow Samba
ufw allow 2222/tcp
ufw allow http
ufw allow 80/tcp
ufw allow 7100:7200/tcp
ufw allow 7100:7200/udp
ufw allow from 64.63.62.61
ufw allow from 64.63.62.61 to any port 22

ufw enable/disable
ufw deny from 23.24.25.0/24
ufw status numbered
ufw delete 2
```


### WELL KNOWN PORTS
```
20 – FTP Data (For transferring FTP data)
21 – FTP Control (For starting FTP connection)
22 – SSH (For secure remote administration which uses SSL to encrypt the transmission)
23 – Telnet (For insecure remote administration)
25 – SMTP (Mail Transfer Agent for e-mail server such as SEND mail)
53 – DNS (Special service which uses both TCP and UDP)
67 – Bootp
68 – DHCP
69 – TFTP (Trivial file transfer protocol uses udp protocol for connection less transmission of data)
80 – HTTP/WWW(Apache)
88 – Kerberos
110 – POP3 (Mail delivery Agent)
123 – NTP (Network time protocol used for time syncing uses UDP protocol)
137 – NetBIOS (nmbd)
139 – SMB-Samba (smbd)
143 – IMAP
161 – SNMP (For network monitoring)
389 – LDAP (For centralized administration)
443 – HTTPS (HTTP+SSL for secure web access)
514 – Syslogd (udp port)
636 – ldaps (both ctp and udp)
873 – rsync
989 – FTPS-data
990 – FTPS
993 – IMAPS
995 – POP3s
1194 – openVPN
1812 – RADIUS
2049 – NFS (nfsd, rpc.nfsd, rpc, portmap)
2401 – CVS server
3306 – MySql
3690 – SVN
6000-6063-X11

nmap 192.168.0.0/24
nmap -p 53 192.168.0.1

# add new IP addr
ip a show
ip a add 192.168.0.100/24 dev eth0  
ping 192.168.0.100
ip a del 192.168.0.100/24 dev eth0

ip addr show dev eth0
ip link set dev eth0 up
ip link set dev eth0 address aa:bb:cc:dd:ee:ff
ip addr add 192.168.1.11/24 dev eth0
route
ip neigh
ifup

service networking restart
```
