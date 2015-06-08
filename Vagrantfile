Vagrant.configure('2') do |config|
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
  end

  # Prepare berks
  [:up, :provision, :destroy].each do |cmd|
    config.trigger.before cmd, stdout: true, force: true, vm: /^phalcon$/ do
      run 'rm -f Berksfile.lock'
      run 'mkdir chef-repo'
      run 'berks package chef-repo/cookbooks.tar.gz'
      run 'tar -C chef-repo -xzf chef-repo/cookbooks.tar.gz'
      run 'pkill -f chef-zero'
    end
  end

  # Install Chef
  config.omnibus.chef_version = :latest
  config.omnibus.install_url = 'http://www.opscode.com/chef/install.sh'

  # Chef-Zero
  config.chef_zero.enabled = true
  config.chef_zero.cookbooks = 'chef-repo/cookbooks'

  # Disabling the Berkshelf plugin
  config.berkshelf.enabled = false

  config.vm.define 'phalcon' do |cs|
    # Set hostname
    cs.vm.hostname = 'phalcon'

    # Every Vagrant virtual environment requires a box to build off of
    cs.vm.box = 'chef/centos-6.5'

    # The url from where the 'config.vm.box' box will be fetched if it
    # doesn't already exist on the user's system
    cs.vm.box_url = 'http://sun4600.cbeagan.org/centos-chef.box'
    #cs.vm.box_url = 'http://vagrant.caringbridge.org/centos-chef.box'

    cs.vm.network :private_network, ip: '33.33.33.50'

    # Chef run to create things
    cs.vm.provision :chef_client do |chef|
      # chef.json = {
      #   'mongodb' => {
      #     'configserver_url' => '33.33.33.50'
      #   }
      # }
      chef.add_recipe 'se-yum::default'
      chef.add_recipe 'role-zendserver'
      chef.add_recipe 'role-BI-apache-phalcon::default'
      chef.add_recipe 'role-mysql::default'
    end
  end
end
