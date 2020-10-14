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
      sudo apt update -y && sudo apt upgrade -y
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

    SHELL
  end

  config.vm.define "master1" do |master1|
    master1.vm.box = "centos/7"
    master1.vm.hostname = 'k8s-master1'
    master1.vm.define vm_name = 'master1'
    master1.vm.network :forwarded_port, guest: 22, host: 2220, id: 'ssh'
    master1.vm.network :private_network, ip: "10.1.7.152"
    master1.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end

    master1.vm.provision "shell", privileged: true, inline: <<-SHELL
      yum -y install epel-release && yum -y update && yum -y install htop iftop iotop vim
      setenforce 0
      sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
      swapoff -a && echo "vm.swappiness=0" >> /etc/sysctl.conf && sysctl -p && free –h
      systemctl disable firewalld && systemctl stop firewalld
      iptables -P FORWARD ACCEPT
      modprobe br_netfilter
      echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
    SHELL
  end

  # config.vm.define "master2" do |master2|
  #   master2.vm.box = "centos/7"
  #   master2.vm.hostname = 'k8s-master2'
  #   master2.vm.define vm_name = 'master2'

  #   master2.vm.network :forwarded_port, guest: 22, host: 2221, id: 'ssh'

  #   master2.vm.network :private_network, ip: "10.1.7.60"
  #   master2.vm.provider :virtualbox do |v|
  #     v.customize ["modifyvm", :id, "--cpus", 2]
  #     v.customize ["modifyvm", :id, "--memory", 2048]
  #     v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
  #   end
  # end

  # config.vm.define "master3" do |master3|
  #   master3.vm.box = "centos/7"
  #   master3.vm.hostname = 'k8s-master3'
  #   master3.vm.define vm_name = 'master3'

  #   master3.vm.network :forwarded_port, guest: 22, host: 2222, id: 'ssh'

  #   master3.vm.network :private_network, ip: "10.1.7.158"
  #   master3.vm.provider :virtualbox do |v|
  #     v.customize ["modifyvm", :id, "--cpus", 2]
  #     v.customize ["modifyvm", :id, "--memory", 2048]
  #     v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
  #   end
  # end

end