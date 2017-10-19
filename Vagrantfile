# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  (1..2).each do |i|
    config.vm.define "node#{i}" do |o|
      o.vm.box = "centos/6"
      o.vm.hostname = "node#{i}"
      o.vm.network "private_network", ip: "172.16.18.#{i}", netmask: "255.240.0.0"
      o.vm.provision :shell, inline: "sed 's/127\.0\.0\.1.*node.*/172\.16\.18\.#{i} node#{i}/' -i /etc/hosts"
      o.vm.provider "virtualbox" do |v|
        v.name = "node#{i}"
        v.memory = 1024
        v.gui = false
      end
    end
  end
end
