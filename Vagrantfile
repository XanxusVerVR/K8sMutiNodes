Vagrant.configure("2") do |config|

  config.vm.define "mainmachine" do |mainmachine|
    mainmachine.vm.box = "ubuntu/bionic64"
    mainmachine.vm.hostname = 'k8s-mainmachine'
    mainmachine.vm.define vm_name = 'mainmachine'
    mainmachine.vm.network :private_network, ip: "10.1.7.189"
    mainmachine.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
    mainmachine.vm.provision "shell", privileged: false, inline: <<-SHELL
      cd ~
      mkdir myDirTestTestTest
      sudo apt-get update -y && apt-get upgrade
      sudo apt install zsh -y
      wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
      sudo chsh -s /bin/zsh vagrant
      zsh
    SHELL
  end

  config.vm.define "master1" do |master1|
    master1.vm.box = "centos/7"
    master1.vm.hostname = 'k8s-master1'
    master1.vm.define vm_name = 'master1'
    master1.vm.network :private_network, ip: "10.1.7.152"
    master1.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end

  config.vm.define "master2" do |master2|
    master2.vm.box = "centos/7"
    master2.vm.hostname = 'k8s-master2'
    master2.vm.define vm_name = 'master2'
    master2.vm.network :private_network, ip: "10.1.7.60"
    master2.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end

  config.vm.define "master3" do |master3|
    master3.vm.box = "centos/7"
    master3.vm.hostname = 'k8s-master3'
    master3.vm.define vm_name = 'master3'
    master3.vm.network :private_network, ip: "10.1.7.158"
    master3.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end

end