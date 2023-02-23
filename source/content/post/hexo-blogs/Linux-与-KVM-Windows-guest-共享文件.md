---
title: Linux Host 与 KVM Windows Guest 共享文件
date: 2022-11-18 19:24:07
description: 互传文件
tags:
    - Linux
---

- #### 将文件从 Linux Host 传到 KVM Windows Guest
    
    - 在 Windows Guest 里面下载安装如下地址的软件
    
        ```bash
        https://www.spice-space.org/download/windows/spice-guest-tools/spice-guest-tools-latest.exe
        ```
    
    - 重启 Windows Guest
    - 发现可以把 Linux 文件拖拽到 Windwos 里面，可是无法反向拖拽
    
</br>

- #### 将文件从 KVM Windows Guest 传到 Linux Host

    - ##### 在 Windows Guest 里面设置共享文件夹
    
        - 新建文件夹（此处在 C盘 根目录下）
        
        ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.37.19.png)
        
        - 设置文件夹共享
        
            - 右键 -> 属性 -> 共享
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.39.56.png)
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.41.28.png)
                
            </br>
            
            - 选择 Everyone -> 添加
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.43.00.png)
                
            </br>
            
            - 选择权限
                
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.45.31.png)
                
            </br>

            - Share
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.46.52.png)
                
            </br>
            
            - 留意这里，图片里面的 "DESKTOP-5J93LDB" 在 Linux mount 操作时将会被换为 Windows 的 IP 地址
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.48.24.png)
                
            </br>

            - 点击 Done 完成共享设置
            
        </br> 
        
        - 设置外部可以访问 Windows
            
            - 回到 Share 的 share 属性页面
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.39.56.png)
                
            </br>  
            
            - 点击 Network and Sharing Center
                
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.54.42.png)
                
            </br>  
            
            - 改变设置如下
            
                ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.19.56.55.png)
            
            </br> 
            
            - 保存设置完成 Windows 端配置
            
    </br> 
    
    - ##### Linux Host 挂载
    
        - 在 Linux Host 下将 Windows Guset 的共享目录挂载到 Linux Host 的某个文件夹下面，然后 cd 到这个文件夹下面就可以访问 share 目录了
        
            ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.20.17.42.png)
            
            ```bash
            sudo mount -t cifs //192.168.122.8/share /home/asleep/share/
            ```
            
            </br>
            
            - "-t cifs" 指定要挂载外部文件系统
            
            </br> 
            
        - 查看 Linux Host 下的文件目录内容，发现已经可以访问到 Windows Guset 的共享目录
        
            ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.18.20.21.08.png)
            
            </br> 
            
        - 要卸载共享，也就是取消挂载，执行如下命令
        
            ```bash
            sudo umount -t cifs //192.168.122.8/share
            ```



