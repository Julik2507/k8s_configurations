# -*- mode: ruby -*-
# vi:set ft=ruby sw=2 ts=2 sts=2:

IMAGE_NAME = "generic/ubuntu2204"
NUM_WORKER_NODES = 2

Vagrant.configure("2") do |config|
  config.vm.box = IMAGE_NAME
  config.vm.boot_timeout = 900
  config.vm.box_check_update = false

  # 1. Control Plane Node
  config.vm.define "controlplane" do |node|
    node.vm.hostname = "controlplane"
    node.vm.network "private_network", ip: "192.168.56.10"

    node.vm.provider "libvirt" do |lv|
      lv.memory = 2048
      lv.cpus = 2
    end
  end

  # 2. Worker Nodes
  (1..NUM_WORKER_NODES).each do |i|
    config.vm.define "node0#{i}" do |node|
      node.vm.hostname = "node0#{i}"
      node.vm.network "private_network", ip: "192.168.56.#{10 + i}"

      node.vm.provider "libvirt" do |lv|
        lv.memory = 2048
        lv.cpus = 1
      end
    end
  end

  # 3. Настройка /etc/hosts и SSH
  config.vm.provision "shell", inline: <<-SHELL
    set -e
    cat <<EOF | sudo tee -a /etc/hosts
192.168.56.10 controlplane
192.168.56.11 node01
192.168.56.12 node02
EOF

    sudo sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
    sudo systemctl restart sshd
  SHELL
end