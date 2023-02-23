---
title: Podman 基本操作
date: 2023-02-11 12:21:23
description: 基本操作（与docker兼容）
tags:
    - Podman
---

- 修改镜像名称
    ```bash
    podman tag
    ```

- 拉取远程镜像
    
    ```bash 
    podman pull
    ```

- 制作镜像
   
    ```bash
	podman commit
	```

- 导出导入镜像
	
	- 保留镜像的层级信息，是一个 docker 的层级目录 tar 包	

		```bash
		# save
		docker save vell001/tf-keras > tf-keras.tar
		# load
		docker load < tf-keras.tar
		```

	- 不会保留镜像的层级信息，体积小，是一个直接的 linux 文件系统 tar 包
	
		```bash
		docker export 33f6c8359187 > tf-keras-33f6c8359187.tar
		docker import tf-keras-33f6c8359187.tar
		```


