---
title: Hexo 文档分类 图片存储
date: 2022-10-23 19:16:39
categories: IT
tags:
    - Hexo
---

**建立分类**<!--more-->
1. 输入 hexo new page 'categories'
######  
2. 在顶层工程目录的 source 目录中會看到 categories 文件夹
######   
3. 修改 categories 中的 index.md 开头，增加一些东西使得变成如下内容

	```markdown
	---
	title: categories
	date: 2022-10-23 13:30:15
	type: "categories"
	---
	```
4. 若要把 "_post" 內的其中一份文档添加到 "Hexo-Usage" categories 里面，在这个文档头部插入 categories: Hexo-Usage

	```markdown
	---
	title: Hexo 基本美化
	date: 2022-10-23 16:08:56
	categories: Hexo-Usage
	---
	```
5. 重新生成静态网页，可以看到导航栏多了一个分类选项

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.23.19.31.09.png)

######   

**建立标签**

和建立分类一样，只不过内容改变一点
######   
1. 输入 hexo new page 'tags'
######   
2. 在顶层工程目录的 source 目录中會看到 tags 文件夹
######   
3. 修改 tags 中的 index.md 开头，增加一些东西使得变成如下内容
	```
	---
	title: tags
	date: 2022-10-23 13:35:49
	type: "tags"
	---
	```
4. 若要把 "_post" 內的其中一份文档添加到 "Hexo" tag 里面，在这个文档头部插入 tags: Hexo
	```
	---
	title: Hexo 文档分类 图片存储
	date: 2022-10-23 19:16:39
	categories: Hexo-Usage
	tags: Hexo
	---
	
	```
5. 插入多个 tags ，记得 tab 缩进
	```
	---
	title: Hexo 文档分类 图片存储
	date: 2022-10-23 19:16:39
	categories: Hexo-Usage
	tags:
		- Hexo
		- Github
	---
	```
6. 重新生成静态网页，可以看到导航栏多了一个标签选项

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.23.19.50.12.png)

**文档里面插入图片**

使用Hexo创建文件搭建博客时，会遇到图片插图，以及插入的图片无法显示的问题

1. 在 Hexo 的目录（也就是顶层工程目录） source 中创建一个图片文件夹，例如 pictures

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.23.19.55.54.png)

######  
2. 把要插入的图片文件放到该目录下面，在文档中正常使用 markdown 的语法插入图片即可，例如

	```
	![img](ictures/xxx.png)
	```

3. 当然，你还可以在 pictures 再创建目录以区分不同文章的图片

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.23.20.00.11.png)

4. 这个时候在文章里面引用的方式就是

	```
	![img](ictures/hexo-文档分类-图片存储/xxx.png)
	```

    记住在 "pictures" 前面有一个 "/" ，表示根目录的意思，因为对于 hexo 来说它的资源文件的根目录就是 source ，当然也可以修改 "_config.yml" 改变这个配置，下图是 hexo 框架文件夹描述

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/20191220164252492.png)




