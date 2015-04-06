# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
HOSTNAME = "dev.slavcopost.com"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box     = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.define HOSTNAME do |host|
  end

  config.vm.hostname = HOSTNAME

  config.ssh.forward_agent = true

  # Create a private network, which allows host-only access to the machine using a specific IP.
  config.vm.network :private_network, ip: "192.168.111.222"

  #syncs entire project directory to ~/application on target machine
  config.vm.synced_folder "../nodejs-hello", "/vagrant"

  config.vm.provider "virtualbox" do |v|
    v.name = HOSTNAME
    v.customize ["modifyvm", :id, "--memory", "1024"]
  end

  #provisions the environment
  config.vm.provision "ansible" do |ansible|
    ansible.raw_arguments = "-i playbooks/hosts"
    ansible.playbook = "playbooks/main.yml"
  end

  config.vm.post_up_message = "
    Your local development environment is set up.
    Access the virtual machine with `vagrant ssh` command, or by ssh.
  "

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.aliases = %w(www.dev.slavcopost.com)
end
