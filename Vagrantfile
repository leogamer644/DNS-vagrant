
Vagrant.configure("2") do |config|
  


  # box configuration
  config.vm.box = "debian/bullseye64"
  config.vm.provision "shell", inline: <<-SHELL
      apt-get update -y
      apt-get upgrade -y
      apt-get -y install bind9 bind9utils bind9-doc
     cp /vagrant/dns/named /etc/default/
     cp /vagrant/dns/named.conf.options /etc/bind/
    SHELL
  # 1st VM tierra/master
  config.vm.define "tierra" do |t|
    t.vm.network "private_network", ip: "192.168.57.103"
    t.vm.hostname="tierra"
    t.vm.provision "shell", inline: <<-SHELL
      cp /vagrant/dns/named.conf.master /etc/bind/named.conf.local
      cp /vagrant/dns/directzone /var/lib/bind/direct.zone
      cp /vagrant/dns/reversezone /var/lib/bind/reverse.zone
      chown bind:bind /var/cache/bind/*
      systemctl restart named
      SHELL
    
  end
  # 2nd VM venus/slave
  config.vm.define "venus" do |v|
    v.vm.network "private_network", ip: "192.168.57.102"
    v.vm.hostname="venus"
    v.vm.provision "shell", inline: <<-SHELL
      cp /vagrant/dns/named.conf.slave /etc/bind/named.conf.local
      systemctl restart named
      SHELL
    
  end

end
