# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = 'bento/ubuntu-16.04'

  # see: https://github.com/chef/bento/issues/682
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end

  config.vm.define 'db' do |host|
    host.vm.network 'private_network', ip: '192.168.103.101'
    host.vm.hostname = "db.django-vagrant-ansible.lh"
  end

  config.vm.define 'app' do |host|
    host.vm.network 'private_network', ip: '192.168.103.100'
    host.vm.hostname = "app.django-vagrant-ansible.lh"
  end
end
