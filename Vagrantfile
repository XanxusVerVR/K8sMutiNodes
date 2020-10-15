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
      sudo apt update -y
      # install git
      sudo apt install zsh git software-properties-common -y
      wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
      sudo chsh -s /bin/zsh vagrant
      zsh

      # clone kubespray
      git clone https://github.com/kubernetes-sigs/kubespray.git -b release-2.14

      # install ansible
      sudo apt-add-repository --yes --update ppa:ansible/ansible
      sudo apt install ansible -y

      # install python3
      sudo echo "deb-src http://archive.ubuntu.com/ubuntu/ bionic main" >> /etc/apt/sources.list
      sudo apt-get update -y
      sudo apt-get build-dep python3.6 -y

      # install pip
      curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
      python get-pip.py
      sudo echo "export PATH=$PATH:$HOME/.local/bin" >> $HOME/.zshrc
      # export PATH=$PATH:$HOME/.local/bin

      # install pip3
      sudo apt-get install python3-pip -y

      # set kubespray
      cd ~/kubespray
      sudo pip3 install -r requirements.txt
      cp -rfp inventory/sample inventory/mycluster
      # set metrics_server_enabled to true
      sed -i 's/metrics_server_enabled: false/metrics_server_enabled: true/g' ~/kubespray/inventory/mycluster/group_vars/k8s-cluster/addons.yml

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