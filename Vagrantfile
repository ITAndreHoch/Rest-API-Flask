# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  config.vm.define "node100" do |node100|
    node100.vm.network "private_network", ip: "192.168.40.10"
    node100.vm.hostname = "node100"
    node100.vm.provider :virtualbox do |vb|
          vb.name = "node100"
    end
    
   node100.vm.provision "ansible_local" do |ansible|
      ansible.install = true
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "node100.yml"
    end
  end

end
