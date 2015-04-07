# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

environment = ENV['ENV'] || 'development'
config_file = './environments/' + environment + '.yaml'

if !File.exist?(config_file)
  puts "Configuration file " + config_file + " missing!"
end

puts "Configuration file " + config_file + " loading!"
CONFIG = YAML.load_file(config_file)

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.hostmanager.enabled           = true
  config.hostmanager.manage_host       = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline   = true

  config.ssh.forward_agent = true
  # config.ssh.insert_key = false

  config.vm.box     = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.provider "virtualbox" do |v|
    v.name = CONFIG['hostname']
    v.customize ["modifyvm", :id, "--memory", "1024"]
  end

  # Set the name of the VM. See: http://stackoverflow.com/a/17864388/100134
  config.vm.define CONFIG['hostname'] do |node|
      # Create a private network, which allows host-only access to the machine using a specific IP.
      node.vm.network :private_network, ip: CONFIG['host']
      node.vm.hostname = CONFIG['hostname']

      node.hostmanager.aliases   = CONFIG['aliases']
  end

  config.vm.synced_folder CONFIG['basedir'], "/vagrant"
  if !CONFIG['synced_folders'].nil?
    CONFIG['synced_folders'].each do |item|
        config.vm.synced_folder item['local'], item['remote'], nfs: true
    end
  end

  # Ansible provisioner
  # https://docs.vagrantup.com/v2/provisioning/ansible.html
  config.vm.provision "ansible" do |ansible|
    ansible.playbook        = "provisioning/playbook.yml"
    ansible.sudo            = true

    # AUTO-GENERATED INVENTORY
    # File is stored .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory
    ansible.groups          = {
      "base_group"          => [CONFIG['hostname']],
      "all_groups:children" => ["base_group"]
    }

    # STATIC INVENTORY
    # ansible.inventory_path  = "provisioning/inventory"

    # ADDITIONAL OPTIONS
    # ansible.extra_vars = {
    #   ntp_server: "pool.ntp.org",
    #   nginx: {
    #     port: 8008,
    #     workers: 4
    #   }
    # }

    # Сan be set to an array of strings corresponding to a list of ansible-playbook arguments
    # (e.g. ['--check', '-M /my/modules'])
    # It is an unsafe wildcard that can be used to apply Ansible options that are not (yet) supported by this Vagrant provisioner
    # ansible.raw_arguments = "-i provisioning/hosts"

    # Only plays, roles and tasks tagged with these values will be executed
    # ansible.tags
    # Only plays, roles and tasks that do not match these values will be executed
    # ansible.skip_tags
  end

  config.vm.post_up_message = "
    Your local development environment is set up.
    Access the virtual machine with `vagrant ssh` command, or by ssh.
  "
end
