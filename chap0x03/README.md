## linux实验三报告
****
## 实验环境：
#### 虚拟机：Virtualbox Ubuntu 20.04 Server 64bit
#### 软件：asciinema，Windows10--powershell
****
## 实验过程记录：
### 1、系统管理：
[![3.2-3.6相关命令的使用](https://asciinema.org/a/RyHukuPQxGZsTINh6kgVGvoqc.svg)](https://asciinema.org/a/RyHukuPQxGZsTINh6kgVGvoqc)
+ 针对上面视频中出现的报错修改：
  
[![针对上面视频中报错内容的设置](https://asciinema.org/a/J9KvLaED27jtLCrXTPjhvJvRe.svg)](https://asciinema.org/a/J9KvLaED27jtLCrXTPjhvJvRe)
***

### 2、Systemd 可以管理所有系统资源。不同的资源统称为 Unit（单位）
#### Unit含义，Unit状态和Unit管理
[![Unit状态和Unit管理](https://asciinema.org/a/pFs8QtAqcoKJ3mbkzXPokCvCm.svg)](https://asciinema.org/a/pFs8QtAqcoKJ3mbkzXPokCvCm)
+ 上面视频操作中出现了报错`Failed to start apache.service: Unit apache.service not found.`，原因是：用`systemctl status`查看系统状态时没有显示`apache.service`服务，应该用存在的服务进行操作，所以在下面的这段视频中解决此问题。

[![问题解决并完成上一个视频没完成的操作](https://asciinema.org/a/GBdOYfldAtRsY4DfjaMEDVghf.svg)](https://asciinema.org/a/GBdOYfldAtRsY4DfjaMEDVghf)
#### Unit依赖关系：A 依赖于 B，就意味着 Systemd 在启动 A 的时候，同时会去启动 B。
[![Unit依赖关系](https://asciinema.org/a/HbIQngKrJIShKwyEKFlTdZbiS.svg)](https://asciinema.org/a/HbIQngKrJIShKwyEKFlTdZbiS)
***
### 3、Unit 的配置文件
#### 每一个 Unit 都有一个配置文件，告诉 Systemd 怎么启动这个 Unit 。
[![概述&配置文件的状态](https://asciinema.org/a/ynWMFsEdRjKaI1Lr7bHLaqdbR.svg)](https://asciinema.org/a/ynWMFsEdRjKaI1Lr7bHLaqdbR)

[![查看配置文件内容](https://asciinema.org/a/ke3pKmMEwc6giLf4H487I0pLT.svg)](https://asciinema.org/a/ke3pKmMEwc6giLf4H487I0pLT)
***
### 4、target
#### 启动计算机的时候，需要启动大量的 Unit。如果每一次启动，都要一一写明本次启动需要哪些 Unit，显然非常不方便。Systemd 的解决方案就是 Target。
[![target](https://asciinema.org/a/xmFtrUjd9TgLfnKiXg73LV8Cy.svg)](https://asciinema.org/a/xmFtrUjd9TgLfnKiXg73LV8Cy)
****
### 5、日志管理
[![日志管理](https://asciinema.org/a/J2L4Sp22DZlXKNmS5dv4ftxYS.svg)](https://asciinema.org/a/J2L4Sp22DZlXKNmS5dv4ftxYS)
****
### 6、开机启动、启动服务、停止服务、读懂配置文件
[![开机启动、启动服务、停止服务、查看配置文件](https://asciinema.org/a/V1II27qJfzYksZjsq6bGmxB8K.svg)](https://asciinema.org/a/V1II27qJfzYksZjsq6bGmxB8K)
+ [查看配置文件→“Unit”区块的启动顺序与依赖关系、“service”区块的启动行为](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)
****
### 7、install区块、target的配置文件、修改配置文件后重启
[![install区块、target的配置文件、修改配置文件后重启](https://asciinema.org/a/Ia8lFQoSjbWBVBXvvinEZj74X.svg)](https://asciinema.org/a/Ia8lFQoSjbWBVBXvvinEZj74X)
****
## 本章完成后的自查清单
1. 如何添加一个用户并使其具备sudo执行程序的权限？
+ 用如下命令添加用户，设置密码和其他个人信息
```
sudo adduser wzl 
```
+ 使其具备sudo执行程序的权限
```
sudo usermod -G sudo -a wzl
```

2. 如何将一个用户添加到一个用户组？
```
usermod -a -G [GroupName] [UserName]
```
3. 如何查看当前系统的分区表和文件系统详细信息？
```
lsblk命令列出系统的所有块设备及其逻辑分区
fdisk命令获取分区列表   sudo fdisk -l
sfdisk命令查看分区    例如：sudo sfdisk -l /dev/sda
parted命令获取硬盘分区，列出大小超过2TB的分区
```
4. 如何实现开机自动挂载Virtualbox的共享目录分区？
+ 用VirtualBox虚拟机的共享文件夹设置共享的本地文件
+ 进入虚拟机Ubuntu系统，打开终端，用root用户操作（sudo -s回车输入密码），首先在虚拟机上创建一个共享目录：`mkdir /mnt/share`
+ 实现挂载 `mount -t vboxsf [Windows共享文件夹名称] /mnt/share`
+ 再次进入`/mnt/share` 目录下就可以看到windows下本地的共享文件了
+ 在文件 `/etc/rc.local` 中（用root用户）追加如下命令`mount -t vboxsf java /mnt/share`,重新开机即可实现自动挂载。
5. 基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量？
```
lvresize --size +{{120G}} --resizefs {{volume_group}}/{{logical_volume}}
lvresize --size {{100}}%FREE {{volume_group}}/{{logical_volume}}
lvextend -L size

lvresize --size -{{120G}} --resizefs {{volume_group}}/{{logical_volume}}
lvreduce -L size
```
6. 如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？
[![操作步骤](https://asciinema.org/a/V96cYAm4xd1qtp6yiROm9J937.svg)](https://asciinema.org/a/V96cYAm4xd1qtp6yiROm9J937)

7. 如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动？实现杀不死？
+ [service]区块的`Restart = always`（总是重启）
+ 重新加载配置文件`sudo systemctl daemon-reload`
+ 重新启动服务`sudo systemctl restart [对应服务]`
****

## 实验小结
1. 本次实验在录屏的基础上对上传的录屏信息进行了重新的编辑，加了标题等信息，看起来更加清晰。
2. Vscode上面的配置遇到了和[语雀](https://www.yuque.com/c4pr1c3/linux)上面一样的问题，通过老师在群里发的解决方法进行解决。
****
## 参考链接
+ [文档1](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
+ [文档2](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)