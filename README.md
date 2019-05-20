Getting Started with Vagrant and Ansible
========================================

To use the vagrant file, you will need to have done the following:

1. Download and Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Download and Install [Vagrant](http://downloads.vagrantup.com/)
3. Install [Ansible](http://www.ansibleworks.com/docs/intro_installation.html)
4. Open a shell prompt (Terminal app on a Mac) and cd into the folder containing the `Vagrantfile`
5. Run the following command to install the necessary Ansible roles for this profile: `$ ansible-galaxy install -r requirements.txt`

Running
-------
Running and provisioning can be handled nicely:

```
$ vagrant up
```

or inject external variables into the VagrantFile during execution

```
$ ENV=local vagrant up
```

You can SSH into the provisioned vm like so:

```
$ vagrant ssh
```

And stop it:

```
$ vagrant halt
```

Done! You have a development environment with the latest Node.js.

Ansible
-------

Ansible is configured to run for the vagrant host
and you can see the specified private IP in `playbooks/hosts`. 

If for some reason you want to use a different IP,
be aware that you will need to update the `Vagrantfile` as well as `playbooks/hosts`.

```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network :private_network, ip: "192.168.111.222"
end
```

Ansible installs the following packages:
* git
* nodejs
* express

Synced Folders
--------------
By default this repo disables directory syncing.
If you wish to have this environment checked in as part of the dev process
you can create your source directory in the top level of the project
and uncomment this line in the `Vagrantfile`:

```ruby
# config.vm.synced_folder "src/", "/vagrant/path/to/your/project"
```

This will sync your source folders over SSH so you can develop on your host machine.

Ansible Variables
-----------------
The file `playbooks/group_vars/all` contains configurations for your install.
This will allow you to configure project directories and things like the port node js will run on.

VM Configuration
----------------
For more on VM configuration options, check out the docs at
[Vagrant](http://docs.vagrantup.com/v2/virtualbox/configuration.html)

PHP: Built-in web server
------------------------

Use `0.0.0.0:8000` for accessing the CLI Web Server from remote machines.
Don't use `$(hostname):8000`, because access from remote machines not works.

```
$ cd /vagrant
$ php -S 0.0.0.0:8000
```
