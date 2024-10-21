# DNS-vagrant

This repository contains a Vagrant configuration file that sets up two virtual machines, one configured as a master DNS server and the other as a slave DNS server, running bind9.

## Overview

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