```
host -t NS zcu.cz
dig -t AAAA zcu.cz
nslookup (IP nebo adresa)
whois

/etc/resolv.conf
/etc/hosts
```

### bind
```
apt-get install bind9 dnsutils
service bind9 start|stop|restart

/etc/bind/named.conf*
/etc/bind/db.*
less /var/log/syslog


dig -t A zcu.cz @127.0.0.1
/etc/bind/db.test.spos
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
/etc/bind/db.192.168.0
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

dig -x 192.168.0.106 @127.0.0.1

```
