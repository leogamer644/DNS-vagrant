# Configuration files for DNS
## Named
```bash
#
# run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-u bind -4" 
# add -4 so that it doesnt try to use ipv6
```
## Named.conf.options
```bash
acl "localnet" {
    192.168.57.0/24;
};

acl "trusted" {
    127.0.0.0/8;
    localnet;
};

options {
        directory "/var/cache/bind";

        recursion yes;
        allow-recursion {
                trusted;
        };
        forwarders {
                208.67.222.222;
         };

        listen-on port 53 {
            192.168.57.103;
            192.168.57.102;
        };

        dnssec-validation yes;
};
```
## named.conf.master
```bash
zone "sistema.test" {
    type master;
    file "/var/lib/bind/direct.zone";
    allow-transfer { 192.168.57.102; };
};

zone "57.168.192.in-addr.arpa" {
    type master;
    file "/var/lib/bind/reverse.zone"; 
    allow-transfer { 192.168.57.102; };
};
```
## Named.conf.slave
```bash
zone "sistema.test" {
    type slave;
    file "/var/lib/bind/direct.zone";
    masters { 192.168.57.103; };
};

zone "57.168.192.in-addr.arpa" {
    type slave;
    file "/var/lib/bind/reverse.zone";
    masters { 192.168.57.103; };
};
```
## directzone
```bash
$TTL	86400
$ORIGIN sistema.test.

@	IN	SOA	tierra.sistema.test. root.sistema.test. (
            2024101902	; Serial
            604800		; Refresh
            86400		; Retry
            2419200		; Expire
            7200        ; Negative Cache TTL
)

; Name servers
@               IN      NS      tierra
@               IN      NS      venus

; A records (IP addresses)
mercurio        IN      A       192.168.57.101
venus           IN      A       192.168.57.102
tierra          IN      A       192.168.57.103
marte           IN      A       192.168.57.104

; CNAME records (aliases)
ns1             IN      CNAME   tierra
ns2             IN      CNAME   venus
mail            IN      CNAME   marte

; MX record (mail exchange)
@               IN      MX      10 marte
```
## reversezone
```bash
$TTL	86400
$ORIGIN 57.168.192.in-addr.arpa.

@	IN	SOA	tierra.sistema.test. root.sistema.test. (
            2024101902	; Serial
            604800		; Refresh
            86400		; Retry
            2419200		; Expire
            7200        ; Negative Cache TTL
)

; Name servers
@               IN      NS      tierra.sistema.test.
@               IN      NS      venus.sistema.test.

; PTR records (reverse lookups)
101     IN      PTR     mercurio.sistema.test.
102     IN      PTR     venus.sistema.test.
103     IN      PTR     tierra.sistema.test.
104     IN      PTR     marte.sistema.test.
```