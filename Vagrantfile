$install_chef = <<SCRIPT
if ! [ -d "/opt/chef" ];
then
    apt-get update
    apt-get install -y curl
    curl -L https://www.opscode.com/chef/install.sh | bash
fi
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.provision :shell, :inline => $install_chef

  config.vm.define "sensu-master" do |sensu_master|
    sensu_master.vm.box = "opscode-centos-6.4"
    sensu_master.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_centos-6.4_provisionerless.box"
    sensu_master.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--name", "sensu-master"]
        vb.customize ["modifyvm", :id, "--memory", "1024"]
     end
     sensu_master.vm.network :forwarded_port, guest: 8080, host: 8080
     sensu_master.vm.provision :chef_solo do |chef|
       chef.cookbooks_path = "cookbooks"
       chef.data_bags_path = "data_bags"
       chef.add_recipe "monitor::master"
       chef.add_recipe "monitor::redis"
       chef.add_recipe "monitor::rabbitmq"
     end
  end

  config.vm.define "sensu-test-client" do |client|
    client.vm.box = "opscode-centos-6.4"
    client.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_centos-6.4_provisionerless.box"
    client.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--name", "sensu-test-client"]
      vb.customize ["modifyvm", :id, "--memory", "512"]
     end
     client.vm.provision :chef_solo do |chef|
       chef.cookbooks_path = "cookbooks"
       chef.data_bags_path = "data_bags"
       chef.add_recipe "monitor::default"
     end     
  end
  
end