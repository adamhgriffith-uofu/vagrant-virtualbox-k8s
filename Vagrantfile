# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 2.3.0"

# Load Ruby Gems:
require 'yaml'

# Environmental Variables:
ENV['HOSTNAME'] = 'vagrant.test'
ENV['VERBOSITY'] = 'v'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Check for vagrant-vbguest plugin
  if !Vagrant.has_plugin?('vagrant-vbguest')
    puts 'ERROR: vagrant-vbguest plugin required. To install run `vagrant plugin install vagrant-vbguest`'
    abort
  end

  # Check for vagrant-disksize plugin
  if !Vagrant.has_plugin?('vagrant-disksize')
    puts 'ERROR: vagrant-disksize plugin required. To install run `vagrant plugin install vagrant-disksize`'
    abort
  end

  # Necessary for mounts to work (box comes with out-of-date kernel)
  config.vbguest.installer_options = { allow_kernel_upgrade: true }

  # Specify the Vagrant Box, version, and update check:
  config.vm.box = "rockylinux/8"
  config.vm.box_version = "8.0.0"
  config.vm.box_check_update = "false"

  # Customize the disksize:
  config.disksize.size = '10GB'

  # Customize the hostname:
  config.vm.hostname = ENV['HOSTNAME']

  # VirtualBox Provider
  config.vm.provider "virtualbox" do |vb|
    # Customize the number of CPUs on the VM:
    vb.cpus = 2

    # Customize the network drivers:
    vb.default_nic_type = "virtio"

    # Display the VirtualBox GUI when booting the machine:
    vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = 8192

    # Customize the name that appears in the VirtualBox GUI:
    vb.name = ENV['HOSTNAME']
  end

  # Run a script on provisioning the box to format the file system:
  config.vm.provision "shell", inline: <<-SHELL
    # Source: https://medium.com/@panda1100/expand-disk-size-of-rocky-linux-8-vagrant-box-for-libvirt-provider-e51b87ef497
    dnf install -y cloud-utils-growpart
    growpart /dev/sda 1
    xfs_growfs /dev/sda1
  SHELL

  # Run Ansible from the Vagrant host:
  config.vm.provision "ansible", run:"always" do |ansible|
    ansible.host_key_checking = false
    ansible.playbook = "./playbook.yml"
    ansible.verbose = ENV['VERBOSITY']
  end


end