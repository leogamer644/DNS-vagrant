# DNS-vagrant

This repository contains a Vagrant configuration file that sets up two virtual machines, one configured as a master DNS server and the other as a slave DNS server, running bind9.

## Overview of "machines" in network

- **Mercurio**: IP: `192.168.57.101`
- **Venus**: Slave DNS server (IP: `192.168.57.102`)
- **Tierra**: Master DNS server (IP: `192.168.57.103`)
- **Marte**: IP: `192.168.57.104`

This setup uses BIND9 for DNS services, where the master server hosts the DNS zones and the slave server synchronizes zone data from the master.

## Getting Started

### Prerequisites

Ensure that you have the following software installed:

- [Vagrant](https://www.vagrantup.com/) (>= 2.0.0)
- [VirtualBox](https://www.virtualbox.org/) or another Vagrant provider
**If you want support**
- [Linux] (any distro really) I dont provide support for windows

### Clone the Repository
```bash
git clone https://github.com/leogamer644/DNS-vagrant.git
cd DNS-vagrant
```
### start vagrant
```bash
vagrant up
```
### Access the VMs
Access one of the VMs through your preferred method, in my case I use 
```bash
vagrant ssh tierra // or vagrant ssh venus
```
### Test the dns
As the solution should be done already you can test it by using the command **dig**
or by using the script provided. you can copy it to the machine by typing the next string of commands in the virtual machine of your choice
```bash
cp /vagrant/script/test.sh test.sh
chmod 777 test.sh
./test.sh 192.168.57.103 // for the master machine
./test.sh 192.168.57.102 // for the slave machine
```
To test that the AXFR exchange has gone through go to the slave machine and type
```bash
dig @192.168.57.102 sistema.test AXFR
```
which should yield a result similar to this next one
``` bash
; <<>> DiG 9.16.50-Debian <<>> @192.168.57.102 sistema.test AXFR
; (1 server found)
;; global options: +cmd
sistema.test.           86400   IN      SOA     tierra.sistema.test. root.sistema.test. 2024101902 604800 86400 2419200 7200
sistema.test.           86400   IN      NS      venus.sistema.test.
sistema.test.           86400   IN      NS      tierra.sistema.test.
sistema.test.           86400   IN      MX      10 marte.sistema.test.
mail.sistema.test.      86400   IN      CNAME   marte.sistema.test.
marte.sistema.test.     86400   IN      A       192.168.57.104
mercurio.sistema.test.  86400   IN      A       192.168.57.101
ns1.sistema.test.       86400   IN      CNAME   tierra.sistema.test.
ns2.sistema.test.       86400   IN      CNAME   venus.sistema.test.
tierra.sistema.test.    86400   IN      A       192.168.57.103
venus.sistema.test.     86400   IN      A       192.168.57.102
sistema.test.           86400   IN      SOA     tierra.sistema.test. root.sistema.test. 2024101902 604800 86400 2419200 7200
;; Query time: 0 msec
;; SERVER: 192.168.57.102#53(192.168.57.102)
;; WHEN: Mon Oct 21 18:58:50 UTC 2024
;; XFR size: 12 records (messages 1, bytes 337)
```