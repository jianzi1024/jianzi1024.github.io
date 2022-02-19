---
layout: post
title: Mac VirtualBox 安装 CentOS
date: 2022-02-19
author: 贱子
tags: [Linux]

---

记录在`Mac`上，使用`VirtualBox 6.1`安装`CentOS 7.9`的过程，以及遇到的问题。

<!--more-->

------

## 0. 原材料

- macOS Big Sur 11.5.2
- VirtualBox 6.1.32
- CentOS镜像：CentOS-7-x86_64-Minimal-2009.iso

## 1. 安装过程

### 1.1 新建虚拟机

1. 创建虚拟机

   ![创建虚拟机](/images/posts/linux/new_machine1.png)

2. 分配内存

   ![创建虚拟机](/images/posts/linux/new_machine2.png)

3. 创建新硬盘

   ![创建虚拟机](/images/posts/linux/new_machine3.png)

4. 选择硬盘类型

   ![创建虚拟机](/images/posts/linux/new_machine4.png)

5. 动态分配空间

   ![创建虚拟机](/images/posts/linux/new_machine5.png)

6. 硬盘文件位置和大小

   ![创建虚拟机](/images/posts/linux/new_machine6.png)

7. 创建成功

   ![创建虚拟机](/images/posts/linux/new_machine7.png)

### 1.2 安装系统

1. 配置虚拟机

   ![创建虚拟机](/images/posts/linux/install_centos1.png)

2. 配置启动顺序，将光驱排到第一

   ![创建虚拟机](/images/posts/linux/install_centos2.png)

3. 配置光驱

   ![创建虚拟机](/images/posts/linux/install_centos3.png)

4. 选择镜像文件（类似于插入光盘），点击“选择虚拟盘”，选中下载的系统镜像（iso文件）

5. 配置完成后，启动虚拟机

   ![创建虚拟机](/images/posts/linux/install_centos5.png)

6. 启动安装程序：上下键选择，回车键选中

   ![创建虚拟机](/images/posts/linux/install_centos6.png)

7. 选择语言：中文（可以点击 left command 键，切换鼠标在虚拟机内，还是主机内）

   ![创建虚拟机](/images/posts/linux/install_centos7.png)

8. 处理分区

   ![创建虚拟机](/images/posts/linux/install_centos8.png)

9. 默认配置，自动分区即可

   ![创建虚拟机](/images/posts/linux/install_centos9.png)

10. 开始安装，设置root密码，如果密码过于简单，则需要点击两次“完成”

    ![创建虚拟机](/images/posts/linux/install_centos10.png)

11. 安装完成，点击重启

    ![创建虚拟机](/images/posts/linux/install_centos11.png)

12. 重启之后，虚拟机会自动弹出光驱里面的iso文件，就可以正常启动系统了。如果没有进入系统，又进入了安装程序，则重新修改启动顺序，将硬盘改为第一位即可。

13. 连接网络：在安装前面配置的时候，有网络选项，可以直接打开，如果忘记打开，则在安装之后，可以自己手动配置。

    ```shell
    ip addr # 查看局域网ip地址，如果没有的话，则网络尚未连接
    cd /etc/sysconfig/network-scripts/
    ls | grep ifcfg # 查看网卡配置
    vi ifcfg-enp0s3 # 修改配置（要看具体电脑上的文件名，如果有多个，则看 ip 命令里有BROADCAST的网卡）
    # 将文件中的 ONBOOT=NO -> YES
    service network restart # 重启网络即可
    ```

14. 将虚拟机的网络方式改为桥接。

### 1.3 使用

1. 安装增强增强工具：目前未安装成功
2. 将虚拟机打开之后，可以通过ssh远程连接，就不用在乎分辨率的问题

## 2. 问题及解决

1. 创建虚拟机后无法打开：不能为虚拟电脑xx打开一个新任务。

   > 这个问题会有很多原因。
   >
   > 1. mac上需要在"系统偏好设置" -> "安全与隐私"中，同意其权限等

2. 



