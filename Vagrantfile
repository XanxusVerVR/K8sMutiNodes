Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    config.vm.hostname = 'k8s-master1'
    config.vm.define vm_name = 'master1'
    config.vm.network :private_network, ip: "10.1.7.152"
    config.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
end