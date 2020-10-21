Vagrant.configure("2") do |config|

  config.vm.define "mainmachine" do |mainmachine|
    mainmachine.vm.box = "ubuntu/bionic64"
    mainmachine.vm.hostname = 'k8s-mainmachine'
    mainmachine.vm.define vm_name = 'mainmachine'
    mainmachine.vm.network :private_network, ip: "10.1.7.189"
    mainmachine.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end

    mainmachine.vm.provision "shell", privileged: false, inline: <<-SHELL
      # Setting Password-less Sudo
      sudo sed -i '/^%admin ALL=(ALL) ALL.*/a vagrant ALL=(ALL) NOPASSWD: ALL' /etc/sudoers

      # install git
      sudo apt install zsh git software-properties-common -y
      wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
      sudo chsh -s /bin/zsh vagrant
      zsh
      # install and set zsh plugin
      git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
      git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
      sed -i 's/plugins=(git)/plugins=(git zsh-syntax-highlighting)/g' ~/.zshrc
      sed -i 's/plugins=(git zsh-syntax-highlighting)/plugins=(git zsh-syntax-highlighting zsh-autosuggestions)/g' ~/.zshrc

      # change theme
      sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="fino"/g' ~/.zshrc

      # clone kubespray & Inventory.ini Dir
      git clone https://github.com/kubernetes-sigs/kubespray.git
      git clone https://github.com/XanxusVerVR/K8sMutiNodes.git

      # install ansible
      sudo apt-add-repository --yes --update ppa:ansible/ansible
      sudo apt install ansible -y

      # install python3
      sudo chmod 707 /etc/apt/sources.list
      sudo echo "deb-src http://archive.ubuntu.com/ubuntu/ bionic main" >> /etc/apt/sources.list
      sudo apt-get update -y
      sudo apt-get build-dep python3.6 -y
      sudo apt install python-minimal -y

      # install pip
      sudo apt-get install python-pip -y

      # install pip3
      sudo apt-get install python3-pip -y

      #generate public key
      ssh-keygen -q -t rsa -N '' -f ~/.ssh/id_rsa <<<y 2>&1 >/dev/null

      # install docker
      sudo apt-get remove docker docker-engine docker.io containerd runc
      sudo apt-get update -y
      sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common -y
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      sudo apt-key fingerprint 0EBFCD88
      sudo add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
      sudo apt-get update -y
      sudo apt-get install docker-ce docker-ce-cli containerd.io -y

      # set kubespray
      cd ~/kubespray
      # commit message: Fix proxy and module_hotfixes, Author: Etienne Champetier <champetier.etienne@gmail.com>, Date:   Tue Oct 20 02:06:07 2020 -0400
      git checkout 03f316e7a242d75db35e7e9f72f3f08a28b188f3
      # install kubespray dependency
      sudo pip3 install -r requirements.txt
      pip install netaddr
      cp -rfp inventory/sample inventory/mycluster
      # set metrics_server_enabled to true
      sed -i 's/metrics_server_enabled: false/metrics_server_enabled: true/g' ~/kubespray/inventory/mycluster/group_vars/k8s-cluster/addons.yml
      CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py 10.1.7.152 10.1.7.60 10.1.7.158
      cp ~/K8sMutiNodes/inventory.ini ~/kubespray/inventory/mycluster
      # execute ansible
      # ansible-playbook -i inventory/mycluster/inventory.ini --become --become-user=root cluster.yml -b -v
    SHELL
  end

  config.vm.define "master1" do |master1|
    master1.vm.box = "centos/7"
    master1.vm.hostname = 'k8s-master1'
    master1.vm.define vm_name = 'master1'
    master1.vm.network :private_network, ip: "10.1.7.152"
    master1.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
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
      v.customize ["modifyvm", :id, "--memory", 4096]
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
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end

end