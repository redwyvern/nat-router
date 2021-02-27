# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = "wyvern-nat-router"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 443, host: 443

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    #vb.gui = true
  
    # Customize the amount of memory on the VM:
    vb.memory = "512"
  end

  config.vm.provision "shell", privileged: false, inline: <<-'SHELL'
      sudo apt-get update
      sudo DEBIAN_FRONTEND=noninteractive apt-get install -y \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common \
        iptables-persistent \
        net-tools
      
      echo net.ipv4.ip_forward=1 | sudo tee -a /etc/sysctl.conf >/dev/null
      sudo sysctl -p /etc/sysctl.conf
      sudo iptables -t nat -A PREROUTING -i enp0s3 -p tcp --dport 443 -j DNAT --to-destination 192.168.86.50
      sudo iptables -t nat -A PREROUTING -i enp0s3 -p tcp --dport 80 -j DNAT --to-destination 192.168.86.50
      sudo iptables -t nat -A POSTROUTING -o enp0s3 -p tcp --dport 443 -j MASQUERADE
      sudo iptables -t nat -A POSTROUTING -o enp0s3 -p tcp --dport 80 -j MASQUERADE
      sudo service netfilter-persistent save
   SHELL
end
