# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  #Ansible Control Server
  config.vm.define "ansibleControlServer" do |acs|
    acs.vm.box = "bento/centos-7"
    acs.vm.hostname = "acs"
    acs.vm.network "private_network", ip: "192.128.20.10"
    #Copy your local folders to remote instance
    acs.vm.provision "file", source: "./", destination: "~/ansible"
  end

  #Web Server-01
  config.vm.define "webserver01" do |web01|
    web01.vm.box = "mwrock/Windows2012R2"
    web01.vm.hostname = "windows-webserver01"
    web01.vm.communicator = "winrm"
    web01.winrm.username = "vagrant"
    web01.winrm.password = "vagrant"
    web01.vm.network "private_network", ip: "192.128.20.11"
    web01.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 2
    end
  end

  # # Linux Server-01
  config.vm.define "LinuxVM1" do |web02|
    web02.vm.box = "bento/centos-7"
    web02.vm.hostname = "CentosVM1"
    web02.vm.network "private_network", ip: "192.128.20.14"
  end

  # Linux Server-02
  config.vm.define "ELK01" do |elk01|
    elk01.vm.box = "bento/centos-7"
    elk01.vm.hostname = "ElkVM1"
    elk01.vm.network "private_network", ip: "192.128.20.12"
  end

  # #Web Server-02
  config.vm.define "ELK02" do |elk02|
    elk02.vm.box = "bento/centos-7"
    elk02.vm.hostname = "ElkVM2"
    elk02.vm.network "private_network", ip: "192.128.20.13"
  end

end
