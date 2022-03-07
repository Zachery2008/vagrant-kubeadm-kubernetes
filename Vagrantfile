NUM_MASTER_NODES=1
NUM_WORKER_NODES=3
IP_NW="10.0.0."
IP_START=10

Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: <<-SHELL
        apt-get update -y
        echo "$IP_NW$((IP_START))  master-node01" >> /etc/hosts
        echo "$IP_NW$((IP_START+1))  master-node02" >> /etc/hosts
        echo "$IP_NW$((IP_START+2))  worker-node01" >> /etc/hosts
        echo "$IP_NW$((IP_START+3))  worker-node02" >> /etc/hosts
        echo "$IP_NW$((IP_START+4))  worker-node03" >> /etc/hosts
    SHELL
    config.vm.box = "bento/ubuntu-21.10"
    config.vm.box_check_update = true

    (1..NUM_MASTER_NODES).each do |i|
      config.vm.define "master0#{i}" do |master|
        master.vm.hostname = "master-node0#{i}"
        master.vm.network "private_network", ip: IP_NW + "#{IP_START + i - 1 }"
        master.vm.provider "virtualbox" do |vb|
            vb.memory = 8192
            vb.cpus = 4
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        end
        master.vm.provision "shell", path: "scripts/common.sh"
        master.vm.provision "shell", path: "scripts/master.sh"
      end
    end

    (1..NUM_WORKER_NODES).each do |i|
      config.vm.define "node0#{i}" do |node|
        node.vm.hostname = "worker-node0#{i}"
        node.vm.network "private_network", ip: IP_NW + "#{IP_START + NUM_MASTER_NODES + i - 1}"
        node.vm.provider "virtualbox" do |vb|
            vb.memory = 8192
            vb.cpus = 4
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        end
        node.vm.provision "shell", path: "scripts/common.sh"
        node.vm.provision "shell", path: "scripts/node.sh"
      end
    end
  end