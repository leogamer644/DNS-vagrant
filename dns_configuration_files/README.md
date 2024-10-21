# Configuration files for DNS
## Named
```bash
#
# run resolvconf?
RESOLVCONF=no # we wont use that

# startup options for the server
OPTIONS="-u bind -4" 
# add -4 so that it doesnt try to use ipv6
```
## Named.conf.options
```bash
acl "localnet" {
    192.168.57.0/24; #over here we add the ip of the network
};

acl "trusted" {
    127.0.0.0/8; #these were the default ones, it makes sense to trust yourself
    localnet;
};

options {
        directory "/var/cache/bind";
# we are adding recursion so that recursive queries are allowed, we must have added the previous acl so that they are the only ones that can do recursive queries
        recursion yes;
        allow-recursion {
                trusted;
        };
# with this configuration typed in it will forward requests it cant answer
        forwarders {
                208.67.222.222;
         };
# make master and slave listen to port 53
        listen-on port 53 {
            192.168.57.103;
            192.168.57.102;
        };
# enable dnssec-validation to make sure the requests have not been changed by anyone
        dnssec-validation yes;
};
```
## named.conf.master
```bash
# makes the master server read the files and acknowledge the zones, so that it takes a direct (name to IP) and reverse (IP to name) and responds
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
# same as the master file but for the slave, so it should just take those files from the master server
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
# direct zone file gives you an ip for the name or a name for an alias.
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
# this one gives you a name for an IP.
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
## Disclaimer
These two last files have the names and IPs of the machines discussed in [Overview](https://github.com/leogamer644/DNS-vagrant/tree/master?tab=readme-ov-file#overview-of-machines-in-network)