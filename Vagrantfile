Vagrant.configure("2") do |config|
  domain = ENV['VAGRANT_DOMAIN'] || "vagrant"
  name   = "vagrant-typo3"
  memory = 512
  cpus   = 1
  ip     = "192.168.190.3"

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "precise64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = nil # set this during provider configuration

  # Detects vagrant-cachier plugin
  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.auto_detect = true
    config.cache.scope = :box
    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
  else
    puts 'WARN:  Vagrant-cachier plugin not detected. Continuing unoptimized.'
  end

  # Use SSH agent forwarding
  config.ssh.forward_agent = true

  config.vm.define name do |config|
    # Set the hostname for this machine
    config.vm.hostname =  "%s.#{domain}" % name.to_s

    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    #config.vm.network "forwarded_port", guest: 80, host: 8080

    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    config.vm.network "private_network", ip: ip

    # For information on available options for the Virtualbox provider, please visit:
    # http://docs.vagrantup.com/v2/virtualbox/configuration.html
    config.vm.provider "virtualbox" do |vbox, override|
      vbox.customize ["modifyvm", :id, "--memory", memory]
      vbox.customize ["modifyvm", :id, "--cpus",   cpus]
      vbox.name = "%s.#{domain}" % name.to_s
      override.vm.box_url = "http://files.vagrantup.com/precise64.box"
    end

    # For information on available options for the VMware provider, please visit:
    # http://docs.vagrantup.com/v2/vmware/configuration.html
    config.vm.provider "vmware" do |vmware, override|
      vmware.vmx['memsize']  = memory
      vmware.vmx['numvcpus'] = cpus
      vmware.vmx['displayName'] = "%s.#{domain}" % name.to_s
      override.vm.box_url = "http://files.vagrantup.com/precise64_vmware.box"
    end

    # For information on available options for Ansible provisioning, please visit:
    # http://docs.vagrantup.com/v2/provisioning/ansible.html
    config.vm.provision "ansible" do |ansible|
      ansible.inventory_path = "hosts"
      ansible.playbook = "site.yml"
      ansible.limit = "development"
      #ansible.verbose = true
    end
  end
end
