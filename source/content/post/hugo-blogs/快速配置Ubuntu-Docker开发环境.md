---
title: "快速配置Ubuntu Docker开发环境"
date: 2023-12-30T10:46:03Z
draft: false
---

#### 下面指令执行之前建议先挂个代理

### 启动一个 Ubuntu 容器
快速创建一个 Ubuntu 容器
```bash
xhost +;\
    podman run -it --net=host -e DISPLAY=$DISPLAY --name=devenv -v /home/hcy/Documents/ubuntu/:/home/devenv/ ubuntu /bin/bash
```

### Ubuntu 容器基本设置
基本设置和基本软件
```bash
apt update;\
    apt upgrade -y;\
    apt install jq xz-utils curl sudo git apt-transport-https ca-certificates ripgrep vim-tiny -y;\
    useradd -m devenv;\
    usermod -s /bin/bash devenv;\
    sudo sh -c 'echo "devenv ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers';\
    sudo chown devenv /home/devenv/;\
    sudo chgrp devenv /home/devenv/;\
    su devenv
```

### 切换到普通用户目录
回到家目录
```bash
cd ~;\
pwd;\
touch .bashrc
```

### 配置 .bashrc
改变 Shell 外观，别名 Neovim ，配置 Shell 代理
```bash
cat >> .bashrc << EOF

PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
alias iv='nvim ~/.config/nvim/init.vim'
alias v='nvim'
alias ls='ls --color'
alias p='export ALL_PROXY=socks5://127.0.0.1:7897'
alias np='unset ALL_PROXY'
    
EOF
```

### 记得开代理先 
打开代理 `p`
```bash
bash;\
source ~/.bashrc;\
p
```

### 拉取最新的 Neovim
apt 安装的版本很旧，直接 github 拉取最新
```bash
cd ~;\
    source .bashrc;\
    response=$(curl -s https://api.github.com/repos/neovim/neovim/releases/latest);\
    version=$(echo "$response" | jq -r '.tag_name');\
    download_url=$(echo "$response" | jq -r '.assets[0].browser_download_url');\
    echo "--- Neovim version: "$version" ----";\
    curl -L -o neovim.tar.gz $download_url;\
    sudo tar -zxvf neovim.tar.gz -C /opt/;\
    unset response;\
    unset version;\
    unset download_url;\
    rm neovim.tar.gz;\
    echo "alias nvim=/opt/nvim-linux64/bin/nvim" >> ~/.bashrc;\
    source ~/.bashrc
```

### 拉取具有 LSP 的 Neovim 配置
配置有 LSP 的
```bash
curl -sL https://raw.githubusercontent.com/HCY-ASLEEP/NVIM-Config/main/nvim-config.sh | sh
```

### 拉取没有 LSP 的 Neovim 配置
配置没有 LSP 的
```bash
curl -sL https://raw.githubusercontent.com/HCY-ASLEEP/NVIM-Config/main/nvim-config-without-lsp/nvim-config.sh | sh
```

### 拉取最新 Node.js
软件仓库里面也是太旧了，需要拉取最新的
```bash
cd ~;\
latest_version=$(curl -sL https://nodejs.org/en/download/ | grep -o -E 'v[0-9]+\.[0-9]+\.[0-9]+' | head -n 1);\
download_url="https://nodejs.org/dist/$latest_version/node-$latest_version-linux-x64.tar.xz";\
install_dir="/opt/nodejs";\
sudo mkdir $install_dir;\
sudo chown -R devenv $install_dir;\
sudo chgrp -R devenv $install_dir;\
curl -o nodejs.tar.xz $download_url;\
tar -xf nodejs.tar.xz -C $install_dir;\
echo -e "\nexport PATH=\$PATH:"$install_dir"/node-"$latest_version"-linux-x64/bin/" >> ~/.bashrc;\
rm nodejs.tar.xz;\
. ~/.bashrc
export PATH=$PATH:$install_dir/node-$latest_version-linux-x64/bin/;\
npm config set registry https://registry.npm.taobao.org;\
npm config set registry https://registry.npm.taobao.org;\
npm i -g yarn;\
unset latest_version;\
unset download_url;\
unset install_dir;\
bash;\
echo "---- Node.js $latest_version installed ----"
```

### 快速安装 miniconda (国内要撤销代理)
清华源安装
```bash
curl https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh -o ~/miniconda.sh;\
    sh ~/miniconda.sh -b;\
    rm ~/miniconda.sh;\
    ~/miniconda3/bin/conda init bash;\
    sed -n '/# >>> conda initialize >>>/,/# <<< conda initialize <<</p' ~/.bashrc >> ~/.condainit;\
    sed -i '/# >>> conda initialize >>>/,/# <<< conda initialize <<</d' ~/.bashrc;\
    echo 'alias cab="source ~/.condainit"' >> ~/.bashrc;\
    . ~/.bashrc
```

### 安装 vim-language-server
安装 vimscript LSP
```url
https://github.com/iamcco/vim-language-server
```
```bash
npm install -g vim-language-server
```

### 安装 pyright language server
通过 pip 安装
```bash
npm install -g pyright
```

### 安装 clangd language server
直接 apt 安装，但是要想获得真正的补全还要安装 gcc 和 g++
```bash
sudo apt install clangd
```

### 安装 black python 格式化工具
格式化 python
```bash
pip install black
```

### 设置容器内输入法变量
这个好像对我没有用
```bash
sudo sh -c 'echo "\nexport GTK_IM_MODULE=fcitx\nexport QT_IM_MODULE=fcitx\nexport XMODIFIERS=@im=fcitx\n" >> /etc/bash.bashrc';\
    . /etc/bash.bashrc
```

### Host 的快速指令设置（需要根据实际修改一下）
配置 .bashrc
```bash
cat >> .bashrc << EOF

PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '

alias p='export ALL_PROXY=socks5://127.0.0.1:7890'
alias np='unset ALL_PROXY'
alias l='curl "http://172.30.255.42:801/eportal/portal/login?user_account=392432&user_password=12542614" ; echo'
alias v='nvim'
alias iv='nvim ~/.config/nvim/init.vim'
alias devenv='xhost + >> /dev/null;\
    podman start devenv;\
    podman exec -it \
        -e DISPLAY=$DISPLAY \
        devenv /bin/bash -c \
        "cd /home/devenv/; su devenv;"'
alias u='sudo apt update; sudo apt upgrade -y;'

EOF
```
