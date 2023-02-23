---
title: Github Pages + Hexo
date: 2022-10-23 13:25:51
description: 白嫖 github 的服务器
tags:
    - Hexo
    - Github
---
**Github Pages 可以为个人博客提供支持，Hexo 可以让 Github Pages 更加美观和提供本地预览，而不需要上传到 Github Pages 之后再看到效果**
###### 
**安装准备**（本地）：
- git
- npm
###### 
**配置 Git**
```bash
git config --global user.name "github 用户名"
git config --global user.email "github 注册邮箱"
```
Github 已经不支持密码登录，需要复杂一点的步骤去验证
###### 
1. 到个人中心-设置-setting
2. ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/75e96721a3344ed5b397ec8adfeedb98.png)
3. ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/4eb29e6a9b2c4bf6b2b5db9299b1a393.png)
   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/3def5390d66a40eab1305013f28383d1.png)
4. 选 classic 的 token 而不是 beta 的，因为 classic 可以永久
5. ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/de366346ddf443fba27a2cda84d9593f.png)
   ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/852ef46dcc3d4018bc79fdac2ed8c917.png)
6. 点击 generate token按钮

###### 
然后 生成了token 一定要复制，不然刷新浏览器就没了
###### 
拿到token以后再去git push/clone ，password就是输入刚才复制的token
###### 
**Hexo 安装（本地全局）**
```bash
npm i hexo-cli -g
```
新建一个文件夹（我的是blogs）用于存放你的博客，然后进入该文件夹，并用如下命令进行初始化并安装必备组件
```bash
git init 
hexo init .
```
初始化后，目录结构如下
```bash
.
 ├── _config.yml 	# 网站配置信息
 ├── package.json 	# 应用程序信息
 ├── scaffolds		# 模板文件夹
 ├── source 		# 存放用户资源
 |   ├── _drafts
 |   └── _posts		# 存放个人博客
 └── themes 		# 主题文件夹
```
然后输入如下命令，然后在浏览器中打开 http://localhost:4000 ，就可以预览原始网站
```bash
hexo new '博客名' 		# 新建博客
hexo g 				# 生成静态网页
hexo s 				# 打开本地服务器
```
然后就可以看到如下的界面
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.23.14.48.30.png)
###### 
**发布到 Github Pages 上面**
###### 
注册 Github 帐号，有帐号了不用注册
###### 
新建一个空仓库，暂时不要创建 README.md ，而且得确保你的仓库是 public 同时，仓库名一定要是 **用户名.github.io**
###### 
在上文提到的 blogs 文件夹下面安装 hexo-deployer-git
```bash
npm install --save hexo-deployer-git
```
###### 
在刚才的博客根目录中的站点配置文件 "_config.yml" ，设置为你的个人仓库名，branch 与你的 git 主分支对应

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-376b7a40b8e6a310cc31bd3522ea9a7a_r.jpg)
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-d15b384267cf4fa326c2e2febb1b2b62_r.png)

###### 
开始推送内容到 Github 上去

```bash
hexo clean 					# 清理缓存
hexo g     					# 将 md 生成 html
git add -A 					# 添加到 git 缓冲区
git commit -m "first time"			# 提交所有更改
hexo d     					# 推送到远程
```
稍等片刻，就可以访问 https://用户名.github.io 了





