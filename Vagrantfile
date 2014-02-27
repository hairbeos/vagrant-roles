# -*- mode: ruby -*-
# vi: set ft=ruby :

# require './vagrant'

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Server roles includes: app, web, db
boxes = [
  { 
    name: 'web', 
    roles: ['web'], 
    ip: '192.168.3.2', 
    vbox_config: [
      { '--memory' => '256' }
    ]
  },
  { 
    name: 'app', 
    roles: ['app'], 
    ip: '192.168.3.3', 
    vbox_config: [
      { '--memory' => '512' }
    ]
  },
  { 
    name: 'db', 
    roles: ['db'], 
    ip: '192.168.3.4', 
    vbox_config: [
      { '--memory' => '256' }
    ]
  }
]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # box setting for all vm
  config.vm.box = 'precise64'
  config.vm.box_url = 'http://files.vagrantup.com/precise64.box'

  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      # vagrant-omnibus
      config.omnibus.chef_version = '11.10.4'

      config.vm.network :private_network, ip: opts[:ip]
      
      # Synced folders
      opts[:synced_folders].each do |hash|
        hash.each do |folder1, folder2|
          config.vm.synced_folder folder1, folder2
        end
      end if opts[:synced_folder]

      # VirtualBox customizations
      unless opts[:vbox_config].nil?
        config.vm.provider :virtualbox do |vb|
          # naming for vm box
          vb.name = "vagrant_#{opts[:name]}"

          # set the hw config
          opts[:vbox_config].each do |hash|
            hash.each do |key, value|
              vb.customize ['modifyvm', :id, key, value]
            end
          end
        end
      end

      config.vm.provision :shell, inline: "echo 'Server: #{opts[:name]} - IP: #{opts[:ip]}'"

      # Configure the box with Chef
      config.vm.provision :chef_solo do |chef|
        # Chef config
        chef.cookbooks_path = 'cookbooks'
        chef.roles_path = 'roles'
        chef.data_bags_path = 'data_bags'

        # Add a Chef roles if specified
        opts[:roles].each do |role|
          chef.add_role(role)   
        end if opts[:roles].kind_of?(Array)
      end
    end 
  end
end
