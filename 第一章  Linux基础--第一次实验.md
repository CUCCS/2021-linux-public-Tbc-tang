# 第一章  Linux基础--第一次实验

### 实验目的

1. 如何配置无人值守安装iso并在Virtualbox中完成自动化安装。
2. Virtualbox安装完Ubuntu之后新添加的网卡如何实现系统开机自动启用和自动获取IP？
3. 如何使用sftp在虚拟机和宿主机之间传输文件？

### 实验内容

##### 实验环境

- 虚拟机：**Virtualbox**
  Ubuntu 20.04 Server 64bit
- 宿主机：**ROG 幻14**
  Windows 10 家庭版

### 实验过程与程序代码

------

### 一、配置无人值守安装iso并在Virtualbox中完成自动化安装

```mm
Focal Fossa 无人值守安装的 iso 制作过程
```

**实现特性**

- 定制一个普通用户名和默认密码

- 定制安装 OpenSSH Server

  **主要操作步骤**

  1、**提前下载好纯净版 Ubuntu 安装镜像 iso 文件**

  此处在[官网](https://releases.ubuntu.com/focal/)下载，然后对比官方文档的sha256文件，对比无误略过图片

  ```
  Get-FileHash 你存放下载的iso镜像的路径
  ```

  2、开始手动安装，需要特别注意的是，因为始终没能解决host-only网卡的问题，我被迫使用NAT网卡，网络地址有些许不同

![](img\install.jpg)

另外需要注意的是，我选择了lvm分组

**正在安装系统，奇怪的是，在某一部上卡死了，等待时间超过了50分钟，检查log发现一个相同的文件被下载了三次**

![](img\installstuck.png)

![](img\installstuck1.png)

**在搜寻解决方法无果后，迫不得已，我在此时选择重启系统，被要求重新装一次系统，本次尝试失败**

##### 3、第二次尝试手动安装

这一次我没有选中lvm分组，一系列操作后继续报错

![](img\reinstall1.png)

![](img\reinstall2.png)

根据第二行，尝试性的按下回车，神奇的事情出现了--系统闪过一堆命令行最后出现了这幅画面

![](img\reinstall3.png)

输入设置的账户和密码cuc后，我成功登录虚拟机，检查ip，一切正常

![](img\reinstall4.png)

至此手动创建成功

### 二、无人值守镜像安装

1、创建一个标准配置的虚拟机，并从老师的课件网站下载focal-init.iso文件

![](img\autoisodownload.png)

2、移除新设置虚拟机的【设置】-【存储】-【控制器：IDE】，在【控制器：SATA】下新建2个虚拟光盘，按顺序挂载纯净版Ubuntu20.04镜像文件，再挂入focal-init.iso文件

3、启动虚拟机，稍等片刻，当出现“Continue with autoinstall? (yes|no)”时输入yes，一段时间后虚拟机就完成配置了

![](img\autoinstallsuccess.png)





### 三、制作自动安装镜像

1、修改user-data

输入：

```
cat /var/log/installer/autoinstall-user-data
```

提示我们Permission denied，这是权限不够，无法访问

再次输入

```
sudo su -
```

并在提示的指示下再次输入密码来获得root权限，回车，输入exit退出权限模式，再输入

```
cat /var/log/installer/autoinstall-user-data
```

此时我们就可以正常访问文件了，为了方便后续修改，输入

```
sudo chow cuc:cuc /var/log/installer/autoinstall-user-data ./
```

切回宿主机，打开cmd，将文件拷贝到主机中，这时出现了问题：NAT网络的IP地址无法和ssh服务联系上

![](img\clone.png)

查看帮助文档，似乎要用host-only网络的网卡才能连上ssh服务，到这里第三项实验基本失败了，因为一些原因，我无法创建本地网卡，只能用NAT网络做替代

