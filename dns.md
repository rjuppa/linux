```
8.8.8.8
1.1.1.1

host -t NS zcu.cz
dig -t AAAA zcu.cz
nslookup (IP nebo adresa)
whois

/etc/resolv.conf
/etc/hosts
```

### bind
```
port 53
netstat -tulpn | grep :53
apt -y install bind9 dnsutils
service bind9 start|stop|restart

vim /etc/resolv.conf
nameserver 127.0.0.1

ls -l /etc/bind/named.conf*
ls -l /etc/bind/db.*
less /var/log/syslog

dig -t A zcu.cz @127.0.0.1

vim /etc/bind/named.conf.local 
zone "test.spos" { 			// zona test.spos
        type master;
        allow-transfer { any; };
        file "/etc/bind/db.test.spos"; 
};

vim /etc/bind/db.test.spos
$TTL	86400
@	IN	SOA	test.spos. root.localhost. (
			      4		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			  86400 )	; Negative Cache TTL
;
@	IN	NS	ns1.test.spos.
@	IN	A	192.168.0.106
mail	IN	A	192.168.0.106
ns1	IN	A	192.168.0.106

@       IN      MX      10      mail

------------------------------
zone "0.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.192.168.0";
};

vim /etc/bind/db.192.168.0			// reverzni zaznam
$ORIGIN 0.168.192.in-addr.arpa.
$TTL	604800
@	IN	SOA	ns1.test.spos. root.localhost. (
			      3		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
0.168.192.in-addr.arpa.	IN	NS	ns1.test.spos.
106.0.168.192.in-addr.arpa.	IN	PTR	mail.test.spos.

nebo
@       IN      NS      ns1.test.spos.
105     IN      PTR     test.spos.


dig -x 192.168.0.106 @127.0.0.1

VIEWS:
acl local {
   192.168.1.0/24;
   localhost;
};
view "localnetwork" {
 match-clients { local; 192.168.0.0/24; }; 
  recursion yes;
  zone "jindra.spos." {
    type master;
    file  "/etc/bind/local/db.jindra.spos"
  };
};

view "publicnetwork"{
match-clients {"any"; }; 
  recursion no;
  zone "jindra.spos." {
    type master;
    file  "/etc/bind/public/db.jindra.spos"
  };
};
```
