# -*- mode: ruby -*-
# vi: set ft=ruby :

$PG1_IP = "172.16.137.102"
$PG2_IP = "172.16.137.103"

Vagrant.configure("2") do |config|
  config.vm.define "pg1" do |pg1|
    pg1.vm.box = "generic/ubuntu1804"
    pg1.vm.hostname = "pg1"
    pg1.vm.network "private_network", ip: $PG1_IP

    pg1.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    pg1.vm.provider :libvirt do |v, override|
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = 2
      v.memory = 4096
    end

    pg1.vm.provision "shell", inline: "apt-get install -y python"
    pg1.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    pg1.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
  end

  config.vm.define "pg2" do |pg2|
    pg2.vm.box = "generic/ubuntu1804"
    pg2.vm.hostname = "pg2"
    pg2.vm.network "private_network", ip: $PG2_IP

    pg2.vm.provider :virtualbox do |v, override|
      v.gui = false
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ["modifyvm", :id, "--cableconnected1", "on"]
      v.customize ["modifyvm", :id, "--cableconnected2", "on"]
    end

    pg2.vm.provider :libvirt do |v, override|
      config.vm.synced_folder ".", "/vagrant", type: "nfs"
      v.cpu_mode = 'custom'
      v.cpu_model = 'kvm64'
      v.volume_cache = 'writeback'
      v.disk_bus = 'virtio'
      v.cpus = 2
      v.memory = 4096
    end

    pg2.vm.provision "shell", inline: "apt-get install -y python"
    pg2.vm.provision "shell", inline: "sysctl net.ipv6.conf.all.disable_ipv6=1"
    pg2.vm.provision "shell", inline: "sysctl net.ipv6.conf.default.disable_ipv6=1"
    pg2.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/site.yml"
      ansible.config_file = "ansible/ansible.cfg"
      ansible.inventory_path = "ansible/inventory"
      ansible.become = true
      ansible.galaxy_role_file = "ansible/roles/requirements.yml"
      ansible.galaxy_roles_path = "/etc/ansible/roles"
      ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"
      ansible.limit = "all"
    end
  end
end
