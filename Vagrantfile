# -*- mode: ruby -*-
# vi: set ft=ruby :
#
$ansible_mode = 'ansible' or 'ansible_local'

VAGRANTFILE_API_VERSION = "2"

VIRTUAL_MACHINES = {
  :master => {
    :ip             => '192.168.9.41',
  },
#  :minion => {
#    :ip             => '192.168.9.42',
#  },

}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.hostmanager.enabled = true
  config.vm.box = "centos/7"
#  config.vm.box = "vStone/centos-7.x-puppet.3.x"
#  config.vm.box_check_update = false

  config.ssh.insert_key = false

  VIRTUAL_MACHINES.each do |name,cfg|

    config.vm.define name do |vm_config|
      vm_config.vm.hostname = name
      vm_config.vm.network :private_network, ip: cfg[:ip]
      vm_config.vm.synced_folder ".", "/vagrant", type: "nfs"
      # check https://www.vagrantup.com/docs/synced-folders/nfs.html

      config.vm.provider :virtualbox do |vb|
        vb.memory = 2048
        vb.cpus = 1
        vb.linked_clone = true

      end # provider

      config.vm.provision $ansible_mode do |ansible|
            ansible.playbook = "main.yml"
            ansible.galaxy_role_file = 'requirements.yml'
            ansible.galaxy_command = "ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}"
            ansible.sudo_user = 'root'
            ansible.sudo = true
            ansible.verbose = "v"

            if ENV['ANSIBLE_TAGS'] then ansible.tags = ENV['ANSIBLE_TAGS']; end

            ansible.groups = {
              'vagrant'   => VIRTUAL_MACHINES.keys,
              'icinga'   =>  [ :master ]
           }
            ansible.limit = 'all'
      end

      if Vagrant.has_plugin?("vagrant-cachier")
          config.cache.synced_folder_opts = {
                type: :nfs,
          }
#          config.cache.scope = :machine
#          config.cache.enabled = false
      end
    end
  end
end

