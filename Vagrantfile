Vagrant.configure("2") do |config|

  config.vm.define "deploy" do |deploy|
    deploy.vm.box = "ubuntu/bionic64"
    deploy.vm.hostname = 'jmeter-ansible-deploy'
    deploy.vm.define vm_name = 'deploy'
    deploy.vm.network :private_network, ip: "10.1.7.190"
    deploy.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end

  config.vm.define "deploy2" do |deploy2|
    deploy2.vm.box = "ubuntu/bionic64"
    deploy2.vm.hostname = 'jmeter-ansible-deploy2'
    deploy2.vm.define vm_name = 'deploy2'
    deploy2.vm.network :private_network, ip: "10.1.7.191"
    deploy2.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end
end
