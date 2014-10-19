# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Make sure our user file exists.
  vagrant_user_file = '.vagrant-user'
  `touch #{ vagrant_user_file }`

  # Read in the user details for this vm.
  vagrant_username = `sed '1q;d' #{ vagrant_user_file } | tr -d '\n'` # Username on line 1
  vagrant_private_key_path = `sed '2q;d' #{ vagrant_user_file } | tr -d '\n'` # Path to private key file on line 2
  vagrant_public_key_path = `sed '3q;d' #{ vagrant_user_file } | tr -d '\n'` # Path to public key file on line 3
  vagrant_do_token = `sed '4q;d' #{ vagrant_user_file } | tr -d '\n'` # Digital Ocean token on line 4

  # look in file and use that username, otherwise use vagrant default.
  if vagrant_username != '' and vagrant_private_key_path != '' and vagrant_public_key_path != ''
    puts "Using found username of '#{ vagrant_username }' from file '#{ vagrant_user_file }'."
    config.ssh.username = vagrant_username
    config.ssh.private_key_path = vagrant_private_key_path
  end

  config.trigger.after [:up], :option => "value" do
    # When we initially run vagrant up we give the following three env variables.
    # After the first provision is complete, we write those to a file.
    if ENV['VAGRANT_SSH_USERNAME'] and ENV['VAGRANT_SSH_PRIVATE_KEY'] and ENV['VAGRANT_SSH_PUBLIC_KEY']
      puts "Writing username of '#{ ENV['VAGRANT_SSH_USERNAME'] }' to file '#{ vagrant_user_file }'."

      output = "'#{ ENV['VAGRANT_SSH_USERNAME'] }\n#{ ENV['VAGRANT_SSH_PRIVATE_KEY'] }\n#{ ENV['VAGRANT_SSH_PUBLIC_KEY'] }\n'#{ ENV['VAGRANT_DO_TOKEN'] }"
      `echo #{ output } > #{ vagrant_user_file }`
    end
  end

  config.trigger.after [:destroy], :option => "value" do
    # When we run vagrant destroy let's also clear out the user file.
    puts "Clearing saved user credentials."
    `echo "" > #{ vagrant_user_file }`
  end

  # The base box that we are going to start with.
  config.vm.box = "chef/fedora-20"

  config.vm.provision "ansible" do |ansible|

    ansible.playbook = "provisioning/playbook.yml"

    # Provision with the saved information or the env variables.
    if ENV['VAGRANT_SSH_USERNAME'] and ENV['VAGRANT_SSH_PRIVATE_KEY'] and ENV['VAGRANT_SSH_PUBLIC_KEY']
      ansible.extra_vars = {
        admin_username: ENV['VAGRANT_SSH_USERNAME'],
        admin_key_path: ENV['VAGRANT_SSH_PUBLIC_KEY']
      }
    elsif vagrant_username != '' and vagrant_private_key_path != '' and vagrant_public_key_path != ''
      ansible.extra_vars = {
        admin_username: vagrant_username,
        admin_key_path: vagrant_public_key_path
      }
    end
  end

  config.vm.provider :digital_ocean do |provider, override|
    if ENV['VAGRANT_DO_TOKEN'] and ENV['VAGRANT_SSH_PRIVATE_KEY']
      provider.token = ENV['VAGRANT_DO_TOKEN']
      provider.image = "Fedora 20 x64"
      provider.region = "sfo1"
      provider.size = "512MB"
      override.ssh.private_key_path = ENV['VAGRANT_SSH_PRIVATE_KEY']
    elsif vagrant_do_token != '' and vagrant_private_key_path != ''
      provider.token = vagrant_do_token
      provider.image = "Fedora 20 x64"
      provider.region = "sfo1"
      provider.size = "512MB"
      override.ssh.private_key_path = vagrant_private_key_path
    end
  end

  # Disabled default shared folder, we don't need this crap.
  config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # Enable provisioning with CFEngine. CFEngine Community packages are
  # automatically installed. For example, configure the host as a
  # policy server and optionally a policy file to run:
  #
  # config.vm.provision "cfengine" do |cf|
  #   cf.am_policy_hub = true
  #   # cf.run_file = "motd.cf"
  # end
  #
  # You can also configure and bootstrap a client to an existing
  # policy server:
  #
  # config.vm.provision "cfengine" do |cf|
  #   cf.policy_server_address = "10.0.2.15"
  # end

  # Enable provisioning with Puppet stand alone.  Puppet manifests
  # are contained in a directory path relative to this Vagrantfile.
  # You will need to create the manifests directory and a manifest in
  # the file default.pp in the manifests_path directory.
  #
  # config.vm.provision "puppet" do |puppet|
  #   puppet.manifests_path = "manifests"
  #   puppet.manifest_file  = "site.pp"
  # end

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  #
  # config.vm.provision "chef_solo" do |chef|
  #   chef.cookbooks_path = "../my-recipes/cookbooks"
  #   chef.roles_path = "../my-recipes/roles"
  #   chef.data_bags_path = "../my-recipes/data_bags"
  #   chef.add_recipe "mysql"
  #   chef.add_role "web"
  #
  #   # You may also specify custom JSON attributes:
  #   chef.json = { mysql_password: "foo" }
  # end

  # Enable provisioning with chef server, specifying the chef server URL,
  # and the path to the validation key (relative to this Vagrantfile).
  #
  # The Opscode Platform uses HTTPS. Substitute your organization for
  # ORGNAME in the URL and validation key.
  #
  # If you have your own Chef Server, use the appropriate URL, which may be
  # HTTP instead of HTTPS depending on your configuration. Also change the
  # validation key to validation.pem.
  #
  # config.vm.provision "chef_client" do |chef|
  #   chef.chef_server_url = "https://api.opscode.com/organizations/ORGNAME"
  #   chef.validation_key_path = "ORGNAME-validator.pem"
  # end
  #
  # If you're using the Opscode platform, your validator client is
  # ORGNAME-validator, replacing ORGNAME with your organization name.
  #
  # If you have your own Chef Server, the default validation client name is
  # chef-validator, unless you changed the configuration.
  #
  #   chef.validation_client_name = "ORGNAME-validator"
end
