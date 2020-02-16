# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box_check_update = false

  # vbox template for all vagranth instances
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "512"
    vb.cpus = 2
  end

  # customize vagrant instance
  config.vm.define "west-01" do |vpncluster|
    vpncluster.vm.box = "bento/ubuntu-19.04"
    vpncluster.vm.provider "virtualbox" do |vb|
      vb.name = "west-01"
     end
     vpncluster.vm.network "private_network", ip: "192.168.1.120"
     vpncluster.vm.network "forwarded_port", guest: 80, host: 81
     vpncluster.vm.provision "shell", inline: <<-SHELL
     apt-get update && apt-get install python3 -y
     SHELL
     vpncluster.vm.provision "ansible_local" do |ansible|
       ansible.playbook = "deploy.yml"
       ansible.become = true
       ansible.compatibility_mode = "2.0"
       ansible.version = "2.9.4"
     end
     vpncluster.vm.provision "shell", inline: <<-SHELL
     apt-get update && apt-get install libgmp3-dev make gcc -y
     export strongSwan_version="5.8.2"
     cd /tmp && wget https://download.strongswan.org/strongswan-$strongSwan_version.tar.bz2
     tar xjvf /tmp/strongswan-$strongSwan_version.tar.bz2
     cd /tmp/strongswan-$strongSwan_version && ./configure --prefix=/usr --sysconfdir=/etc && make && make install
     echo "===================================================================================="
                               hostnamectl status
     echo "===================================================================================="
     echo "         \   ^__^                                                                  "
     echo "          \  (oo)\_______                                                          "
     echo "             (__)\       )\/\                                                      "
     echo "                 ||----w |                                                         "
     echo "                 ||     ||                                                         "
     SHELL


  end

  # customize vagrant instance
  config.vm.define "east-01" do |vpncluster|
    vpncluster.vm.box = "bento/ubuntu-19.04"
    vpncluster.vm.network "private_network", ip: "192.168.1.121"
    vpncluster.vm.network "forwarded_port", guest: 80, host: 82
    vpncluster.vm.provider "virtualbox" do |vb|
      vb.name = "east-01"
     end
    vpncluster.vm.provision "shell", inline: <<-SHELL
    apt-get update && apt-get install python3 -y
    SHELL
     vpncluster.vm.provision "ansible_local" do |ansible|
       ansible.playbook = "deploy.yml"
       ansible.become = true
       ansible.compatibility_mode = "2.0"
       ansible.version = "2.9.4"
     end
    vpncluster.vm.provision "shell", inline: <<-SHELL
    apt-get update && apt-get install libgmp3-dev make gcc -y
    export strongSwan_version="5.8.2"
    cd /tmp && wget https://download.strongswan.org/strongswan-$strongSwan_version.tar.bz2
    tar xjvf /tmp/strongswan-$strongSwan_version.tar.bz2
    cd /tmp/strongswan-$strongSwan_version && ./configure --prefix=/usr --sysconfdir=/etc && make && make install
    echo "===================================================================================="
                              hostnamectl status
    echo "===================================================================================="
    echo "         \   ^__^                                                                  "
    echo "          \  (oo)\_______                                                          "
    echo "             (__)\       )\/\                                                      "
    echo "                 ||----w |                                                         "
    echo "                 ||     ||                                                         "
    SHELL
  end




end
