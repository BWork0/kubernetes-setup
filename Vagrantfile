# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  nodes = [
     { name: "swarm00", ip: "10.10.10.100" },
     { name: "swarm01", ip: "10.10.10.101" }
  ]
 
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
 
  nodes.each do |node|
     config.vm.define node[:name] do |swarm|
      swarm.vm.box = "generic/ubuntu2304"
      swarm.vm.hostname = "#{node[:name]}.node.com"
      swarm.vm.network :private_network, ip: node[:ip]
      config.vm.provider "virtualbox" do |v|
      end
      swarm.vm.provision "file", source: "nginx.yaml", destination: "nginx.yaml"
      swarm.vm.provision "shell", inline: <<-SHELL
        echo Hier
        sudo ufw disable
      SHELL
      swarm.vm.provision "shell", inline: <<-SHELL
        if [ "$(hostname)" == "swarm00" ]; then
          curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik --flannel-backend=host-gw --tls-san=10.10.10.100 --bind-address=10.10.10.100 --advertise-address=10.10.10.100 --node-ip=10.10.10.100 --cluster-init" sh -s -
          systemctl status k3s
          sudo chmod 644 /etc/rancher/k3s/k3s.yaml
          JOIN_TOKEN=$(sudo cat /var/lib/rancher/k3s/server/node-token)
          echo "JOIN_COMMAND= curl -sfL https://get.k3s.io | K3S_URL=https://10.10.10.100:6443 K3S_TOKEN=$JOIN_TOKEN sh -" > /vagrant/join_command.env
        else
          source /vagrant/join_command.env
        fi
      SHELL
    end
  end
 end
