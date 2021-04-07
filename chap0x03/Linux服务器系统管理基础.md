# Linux服务器系统管理基础

## 实验目的

自学Systemd，掌握基本的相应命令，加深对服务器系统管理的理解

## 实验环境

Virtual Box 虚拟机：Ubuntu 20.04 Linux

宿主机：win10 cmd

## 实验内容

Systemd入门教程：命令篇 http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html

Systemd入门教程：实战篇 http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html

通过asciinema录制并上传

### 实验具体内容

#### **命令篇**

**一/二、init方法与查看版本号**

需要特别注意的是：

1、systemctl是 Systemd 的主命令，用于管理系统，相关的重启系统、关闭系统等命令操作过后录屏进程同样会结束，所以在录屏中没有体现出来。

[![asciicast](https://asciinema.org/a/405206.svg)](https://asciinema.org/a/405206)

2、在改变时间时有报错，经过查询，需要先关闭时间同步

```systemd
$ timedatectl set-ntp no
```

输入密码，执行如下命令：

```
$ sudo timedatectl set-time 2021-3-29
$ timedatectl set-ntp yes
```

完成修改！

**三、系统管理**

除3.1无法录制外，其他相应操作都有

[![asciicast](https://asciinema.org/a/405209.svg)](https://asciinema.org/a/405209)

**四、Unit**

[![asciicast](https://asciinema.org/a/405212.svg)](https://asciinema.org/a/405212)

**五、Unit 的配置文件**

[![asciicast](https://asciinema.org/a/405217.svg)](https://asciinema.org/a/405217)

**六、Target**

[![asciicast](https://asciinema.org/a/405220.svg)](https://asciinema.org/a/405220)

**七、日志管理**

注：以下代码执行后无法退出，命令无反应

```bash
# 实时滚动显示最新日志
$ sudo journalctl -f
$ sudo journalctl -u nginx.service -f
```

另外，`Failed to parse vacuum size: = `出现的这个问题，是格式问题

`sudo journalctl --vacuum-size = 1G`这一命令不能等号前后不能出现空格

[![asciicast](https://asciinema.org/a/405226.svg)](https://asciinema.org/a/405226)



#### **实战篇**

**一&二&三、开机&启动、停止服务**

[![asciicast](https://asciinema.org/a/405460.svg)](https://asciinema.org/a/405460)

**四、读懂配置文件**

[![asciicast](https://asciinema.org/a/405465.svg)](https://asciinema.org/a/405465)

**七&八&九、[Install] 区块、Target 的配置文件、修改配置文件后重启**

[![asciicast](https://asciinema.org/a/405479.svg)](https://asciinema.org/a/405479)



### 本章自查清单

1. 如何添加一个用户并使其具备sudo执行程序的权限？

   - 用adduser命令创建用户，然后把新建的用户加入到sudo组里面,就可以看到新建用户在sudo组里面了

   - ```
     sudo adduser tbc
     sudo usermod -G sudo -a tbc
     ```

     ```
     cuc@rhel7:~$ sudo adduser tbc
     [sudo] password for cuc:
     Adding user `tbc' ...
     Adding new group `tbc' (1001) ...
     Adding new user `tbc' (1001) with group `tbc' ...
     Creating home directory `/home/tbc' ...
     Copying files from `/etc/skel' ...
     New password:
     Retype new password:
     passwd: password updated successfully
     Changing the user information for tbc
     Enter the new value, or press ENTER for the default
             Full Name []: tbc
             Room Number []:
             Work Phone []:
             Home Phone []:
             Other []:
     Is the information correct? [Y/n] y
     cuc@rhel7:~$ sudo usermod -G sudo -a tbc
     cuc@rhel7:~$ sudo cat /etc/group
     root:x:0:
     daemon:x:1:
     bin:x:2:
     sys:x:3:
     adm:x:4:syslog,cuc
     tty:x:5:syslog
     disk:x:6:
     lp:x:7:
     mail:x:8:
     news:x:9:
     uucp:x:10:
     man:x:12:
     proxy:x:13:
     kmem:x:15:
     dialout:x:20:
     fax:x:21:
     voice:x:22:
     cdrom:x:24:cuc
     floppy:x:25:
     tape:x:26:
     sudo:x:27:cuc,tbc
     audio:x:29:
     dip:x:30:cuc
     www-data:x:33:
     backup:x:34:
     operator:x:37:
     list:x:38:
     irc:x:39:
     src:x:40:
     gnats:x:41:
     shadow:x:42:
     utmp:x:43:
     video:x:44:
     sasl:x:45:
     plugdev:x:46:cuc
     staff:x:50:
     games:x:60:
     users:x:100:
     nogroup:x:65534:
     systemd-journal:x:101:
     systemd-network:x:102:
     systemd-resolve:x:103:
     systemd-timesync:x:104:
     crontab:x:105:
     messagebus:x:106:
     input:x:107:
     kvm:x:108:
     render:x:109:
     syslog:x:110:
     tss:x:111:
     uuidd:x:112:
     tcpdump:x:113:
     ssh:x:114:
     landscape:x:115:
     lxd:x:116:cuc
     systemd-coredump:x:999:
     cuc:x:1000:
     ssl-cert:x:117:
     tbc:x:1001:
     ```

     如何将一个用户添加到一个用户组？

     - usermod -a -G groupA user 需要加上-a参数，否则会使你离开其他用户组，仅仅做为用户组 groupA 的成员。

2. 如何将一个用户添加到一个用户组？

   usermod -a -G groupA user 需要加上-a参数，否则会使你离开其他用户组，仅仅做为用户组 groupA 的成员。

3. 如何查看当前系统的分区表和文件系统详细信息？

   - df -hT 只可以查看已经挂载的分区和文件系统类型。
   - fdisk -l 可以显示出所有挂载和未挂载的分区，但不显示文件系统类型。
   - gdisk -l 同样也可
   - parted -l 可以查看未挂载的文件系统类型，以及哪些分区尚未格式化。大于2TB分区支持使用此
   - lsblk -f 也可以查看未挂载的文件系统类型

4. 如何实现开机自动挂载Virtualbox的共享目录分区？

   -  创建一个共享文件夹的挂载目录：`mkdir /mnt/share`
   - 用以下命令实现挂载：`mount-t vboxsf share_file_name /mnt/share`
   - 进入/etc/fstab进行编辑:`share_file_name /mnt/share vboxsf default 0 0`

5. 基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量？

- 创建分区并修改为LVM模式:` fdisk 分区名`
- 动态扩容: `lvextend -L {{size}}`
- 缩减容量: `lvreduce -L {{size}}`

6. 如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？

- 修改systemd-networkd中的Service
- ExecStartPost=网络联通时运行的指定脚本
- ExecStopPost=网络断开时运行的另一个脚本

7. 如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动，实现杀不死？

* 以下命令：

  ```
  sudo systemctl vi scriptname
  restart = always
  sudo systemctl daemon-reload
  ```

  

### 参考文献

[Systemd 入门教程：实战篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)

[Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)