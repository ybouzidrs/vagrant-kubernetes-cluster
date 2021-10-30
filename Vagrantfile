# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure(2) do |config|


  # Kubernetes Controlplane Server
  config.vm.define "controlplane" do |controlplane|
    controlplane.vm.box = "centos/7"
    controlplane.vm.hostname = "controlplane.example.com"
    controlplane.vm.network "private_network", ip: "172.42.42.100"
    controlplane.vm.provider "virtualbox" do |v|
      v.name = "controlplane"
      v.memory = 2048
      v.cpus = 2
    end
    controlplane.vm.provision "docker-setup", type: "file", source: "./scripts/setup-container.sh", destination: "~/setup-container.sh"
    controlplane.vm.provision "kubetools-setup", type: "file", source: "./scripts/setup-kubetools.sh", destination: "~/setup-kubetools.sh"
    controlplane.vm.provision "inline", type: "shell", inline: <<-SHELL
    sudo su
    chmod +x *.sh
    ./setup-container.sh
    ./setup-kubetools.sh
    SHELL
  end

  NodeCount = 3

  # Kubernetes Worker Nodes
  (1..NodeCount).each do |i|
    config.vm.define "worker#{i}" do |workernode|
      workernode.vm.box = "centos/7"
      workernode.vm.hostname = "worker#{i}.example.com"
      workernode.vm.network "private_network", ip: "172.42.42.10#{i}"
      workernode.vm.provider "virtualbox" do |v|
        v.name = "worker#{i}"
        v.memory = 1024
        v.cpus = 1
      end
      workernode.vm.provision "docker-setup", type: "file", source: "./scripts/setup-container.sh", destination: "~/setup-container.sh"
      workernode.vm.provision "kubetools-setup", type: "file", source: "./scripts/setup-kubetools.sh", destination: "~/setup-kubetools.sh"
      workernode.vm.provision "worker-inline", type: "shell", inline: <<-SHELL
        sudo su
        chmod +x *.sh
        ./setup-container.sh
        ./setup-kubetools.sh
      SHELL
    end
  end
end
