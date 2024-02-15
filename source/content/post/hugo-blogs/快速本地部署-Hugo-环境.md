---
title: "快速本地部署 Hugo 环境"
date: 2023-12-30T11:29:35Z
draft: false
---

**进入要存放博客文件的目录之后才可以执行以下指令**

#### 拉取安装最新Hugo
```bash
response=$(curl -s https://api.github.com/repos/gohugoio/hugo/releases/latest);\
    version=$(echo "$response" | jq -r '.tag_name');\
    download_url=$(echo "$response" | jq -r '.assets[-5].browser_download_url');\
    echo "--- Hugo version: "$version" ----";\
    curl -L -o hugo.deb $download_url;\
    sudo apt install ./hugo.deb;\
    rm hugo.deb;\
    unset response;\
    unset version;\
    unset download_url
```

#### 拉取博客源数据
```bash
git config --global init.defaultBranch main;\
    git config --global user.name "hcy-asleep";\
    git config --global user.email 2420066864@qq.com;\
    git config  --global credential.helper store;\
    git clone https://github.com/HCY-ASLEEP/Hugo_Blog_Source.git;\
    mv Hugo_Blog_Source/ blogs/;\
    mkdir blogs/source/themes/;\
    git clone https://github.com/adityatelange/hugo-PaperMod.git blogs/source/themes/PaperMod/;\
    mkdir blogs/source/public/;\
    cp blogs/source/autogit blogs/source/public/autogit;
    sed -i '1 a script_dir=$(pwd)\ncd ..\nhugo\ncd $script_dir' blogs/source/public/autogit;\
    echo "autogit" >> blogs/source/public/.gitignore;\
    cd blogs/source/public/;\
    git init;\
    git remote add origin https://github.com/HCY-ASLEEP/HCY-ASLEEP.github.io.git;\
    cp ../google188014ab03dc55b7.html .
    cd ..
```

