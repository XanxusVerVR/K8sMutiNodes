Vagrant.configure("2") do |config|

  config.vm.define "deploy" do |deploy|
    deploy.vm.box = "ubuntu/bionic64"
    deploy.vm.hostname = 'k8s-deploy'
    deploy.vm.define vm_name = 'deploy'
    deploy.vm.network :private_network, ip: "10.1.7.189"
    # deploy.vm.network "forwarded_port", guest: 80, host: 80
    # deploy.vm.network "forwarded_port", guest: 443, host: 443
    deploy.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end

    deploy.vm.provision "shell", privileged: false, inline: <<-SHELL
      # Setting Password-less Sudo
      sudo sed -i '/^%admin ALL=(ALL) ALL.*/a vagrant ALL=(ALL) NOPASSWD: ALL' /etc/sudoers
      sudo sed -i '1i Defaults  editor=/usr/bin/vim' /etc/sudoers
      # set timezone
      sudo timedatectl set-timezone Asia/Taipei

      sudo apt-get update -y

      # install git
      sudo apt install zsh git unzip software-properties-common jq -y
      git config --global user.name "Mr. Dai"
      git config --global user.email xanxus@example.com
      git config --global core.editor "vim"
      wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
      sudo chsh -s /bin/zsh vagrant
      zsh
      # install and set zsh plugin
      git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
      git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
      sed -i 's/plugins=(git)/plugins=(git zsh-syntax-highlighting zsh-autosuggestions)/g' ~/.zshrc

      # change theme
      sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="fino"/g' ~/.zshrc

      # clone kubespray & Inventory.ini Dir & K8s Course
      git clone https://github.com/kubernetes-sigs/kubespray.git
      git clone https://github.com/XanxusVerVR/K8sMutiNodes.git
      git clone https://github.com/hwchiu/hiskio-course.git

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

      # install docker compose
      sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      sudo chmod +x /usr/local/bin/docker-compose

      # install kubectl
      sudo apt-get install -y apt-transport-https gnupg2
      curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
      echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
      sudo apt-get update -y
      sudo apt-get install kubectl -y
      sudo kubectl completion zsh > ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/_kubectl
      echo "alias k='kubectl'" >> ~/.zshrc
      echo "alias kd='kubectl describe'" >> ~/.zshrc
      echo 'complete -F __start_kubectl k' >> ~/.zshrc

      # install java maven gradle helm
      wget https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u272-b10/OpenJDK8U-jdk_x64_linux_hotspot_8u272b10.tar.gz --directory-prefix=/tmp
      wget https://services.gradle.org/distributions/gradle-6.7-bin.zip --directory-prefix=/tmp
      wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz --directory-prefix=/tmp
      wget https://get.helm.sh/helm-v3.4.0-linux-amd64.tar.gz --directory-prefix=/tmp
      sudo tar zxvf /tmp/OpenJDK8U-jdk_x64_linux_hotspot_8u272b10.tar.gz -C /usr/share
      sudo unzip /tmp/gradle-6.7-bin.zip -d /usr/share
      sudo tar zxvf /tmp/apache-maven-3.6.3-bin.tar.gz -C /usr/share
      sudo tar zxvf /tmp/helm-v3.4.0-linux-amd64.tar.gz -C /usr/share
      sudo cp /usr/share/linux-amd64/helm /usr/local/bin/
      sudo helm completion zsh > ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/_helm
      sed -i "$ a export HELM_EXPERIMENTAL_OCI=1" ~/.zshrc
      sed -i "$ a export PATH=/usr/share/jdk8u272-b10/bin:/usr/share/gradle-6.7/bin:/usr/share/apache-maven-3.6.3/bin:/usr/share/go/bin:$PATH" ~/.zshrc

      # install go
      wget https://golang.org/dl/go1.15.4.linux-amd64.tar.gz --directory-prefix=/tmp
      sudo tar -xzf /tmp/go1.15.4.linux-amd64.tar.gz -C /usr/share
      source ~/.zshrc

      # install kubecolor
      go get -u github.com/dty1er/kubecolor/cmd/kubecolor
      sudo cp ~/go/bin/kubecolor /usr/local/bin
      echo "alias kc='kubecolor'" >> ~/.zshrc
      echo "alias kcd='kubecolor describe'" >> ~/.zshrc

      # install kind
      curl -Lo ./kind "https://kind.sigs.k8s.io/dl/v0.9.0/kind-$(uname)-amd64"
      chmod +x ./kind
      sudo cp ./kind /usr/local/bin
cat << EOF > config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
EOF

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

      # set vimrc
      git clone https://github.com/morhetz/gruvbox.git ~/gruvbox
      git clone https://github.com/vim-airline/vim-airline.git /tmp/vim-airline
      mkdir -p ~/.vim/colors
      cp -r /tmp/vim-airline/. ~/.vim
      cp ~/gruvbox/colors/gruvbox.vim ~/.vim/colors
      echo "~/gruvbox/gruvbox_256palette.sh" >> ~/.zshrc
cat << EOF > ~/.vimrc
syntax enable "開啟語法高亮度
set nu "顯示行號
set encoding=utf-8 "讓vim內部有辦法使用使用所有的字元
set fileencodings=utf-8,utf-16,big5,gb2312,gbk,gb18030,euc-jp,euc-kr,latin1 "設定其在開啟文字檔時要優先使用什麼字元編碼方式來開啟
set smartindent "基於autoindent之上，針對「{」(左大括號)後所產生的新行，再多往內縮排一次
set expandtab "Tab會轉換成space字元
set tabstop=2 "定義在vim中，Tab寬度要有幾個字元
set softtabstop=2 "定義一個Tab單位，不足的部分要用幾個空白字元補齊
set shiftwidth=2 "設定自動縮排產生的字元寬度
set smarttab "讓在行首插入縮排字元時，也能使用「shiftwidth」環境變數所定義的寬度
set noconfirm "關閉確認功能
set backspace=indent,eol,start "允許使用「ctrl+w」和「ctrl+u」快速鍵來刪除獨立詞語和同類字元(縮排、非縮排字元)
set ruler "讓右下角顯示座標
set t_Co=256 "能支援256色
set hlsearch "設定高亮度顯示搜尋結果
set incsearch "在關鍵字還沒完全輸入完畢前就顯示結果
set cursorline "顯示游標所在的row
set cursorcolumn "顯示游標所在的column
set ignorecase "忽略大小寫搜尋
set showcmd "開啟右下角顯示目前指令輸入到哪
set showmode "開啟vim的模式提示
set wrap "自動換行
set autowrite "當vim編輯器失去焦點，就會自動存檔
autocmd BufWritePre * :%s/\s\+$// "儲存時移除行尾空白
let g:gruvbox_contrast_dark = 'hard'
set background=dark
colorscheme gruvbox "顏色主題
hi CursorLine cterm=bold ctermbg=054 ctermfg=White
hi CursorColumn cterm=bold ctermbg=052 ctermfg=White
" ctermbg為背景色 ctermfg為前景色(字體顏色) cterm為字體形式
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#formatter = 'default'

"#######################################################
" encode
if has("multi_byte")
    set fileencodings=utf-8,utf-16,big5,gb2312,gbk,gb18030,euc-jp,euc-kr,latin1
else
    echoerr "If +multi_byte is not included, you should compile Vim with big features."
endif
EOF
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

  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "ubuntu/bionic64"
    ubuntu.vm.hostname = 'ubuntu'
    ubuntu.vm.define vm_name = 'ubuntu'
    ubuntu.vm.network :private_network, ip: "10.1.7.111"
    ubuntu.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
    end
  end
end