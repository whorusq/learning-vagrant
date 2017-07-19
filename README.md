## 基于 Vagrant 的 LAMP 开发环境搭建

![Vagrant](./vagrant.png)

---

### 索引

1. [Vagrant 介绍](#1-vagrant-介绍)
2. [安装 Vagrant 和 VirtualBox](#2-安装-vagrant-和-virtualbox)
	- 2.1. [安装 VirtualBox（支持 Windows/macOS/Linux）](#21-安装-virtualbox支持-windowsmacoslinux)
	- 2.2. [安装 Vagrant（支持 Windows/macOS/Debian/CentOS）](#22-安装-vagrant支持-windowsmacosdebiancentos)
3. [配置、启动 Vagrant](#3-配置启动-vagrant)
	- 3.1. [增加一个 box](#31-增加一个-box)
	- 3.2. [初始化、启动](#32-初始化启动)
	- 3.3. [ssh 到虚拟机](#33-ssh-到虚拟机)
4. [配置 LAMP](#4-配置-lamp)
5. [附录](#5-附录)
	- 5.1. [常用命令](#51-常用命令)
	- 5.2. [Vagrantfile 常用配置](#52-vagrantfile-常用配置)
	- 5.3. [解决 mount: unknown filesystem type 'vboxsf'](#53-解决-mount-unknown-filesystem-type-vboxsf)
	- 5.4. [关闭静态文件缓存](#54-关闭静态文件缓存)

[vagrant-homepage]: https://www.vagrantup.com "Vagrant homepage"
[vagrant-docs]: https://www.vagrantup.com/docs "Vagrant docs"
[vagrant-box]: https://atlas.hashicorp.com/boxes/search "Vagrant box"
[vagrant-box-thd]: http://www.vagrantbox.es "Vagrant box"
[vagrant-docker]: https://www.zhihu.com/question/32324376 "Vagrant Docker"
[virtualbox-download]: https://www.virtualbox.org/wiki/Downloads "Virtualbox Download"
[vagrant-download]: https://www.vagrantup.com/downloads.html "Vagrant Download"

---

### 1. Vagrant 介绍

相关资源

* 官网：[https://www.vagrantup.com][vagrant-homepage]
* 文档：[https://www.vagrantup.com/docs][vagrant-docs]
* 官方 box 仓库：[https://atlas.hashicorp.com/boxes/search][vagrant-box]
* 第三方 box 仓库：[http://www.vagrantbox.es][vagrant-box-thd]

下面是节选自官方对 Vagrant 的说明：

> **WHY VAGRANT?**
>
> Vagrant provides easy to configure, reproducible, and portable work environments built on top of industry-standard technology and controlled by a single consistent workflow to help maximize the productivity and flexibility of you and your team.
>
> To achieve its magic, Vagrant stands on the shoulders of giants. Machines are provisioned on top of **VirtualBox**, **VMware**, **AWS**, or any other provider. Then, industry-standard provisioning tools such as shell scripts, Chef, or Puppet, can be used to automatically install and configure software on the machine.

其实，说白了 Vagrant 就是一个普普通通的装了 Linux 的 VirtualBox 虚拟机，配以 Vagrant  团队为之开发的一系列套件，辅助完成诸如安装初始化、文件同步、ssh、部署环境升级、功能插件安装等等一些列问题的开发环境部署套件。（参见知乎话题：[Vagrant 和 Docker的使用场景和区别?][vagrant-docker]

解决的痛点：

* 开发环境快速部署
* 开发环境更迭

### 2. 安装 Vagrant 和 VirtualBox

> 官方说明中，Vagrant 是支持 VirtualBox/VMware/AWS 等虚拟软件的，选择 VirtualBox 主要是因为开源、免费
>
> 安装过程都是傻瓜化的，一路下一步即可

#### 2.1. 安装 VirtualBox（支持 Windows/macOS/Linux）

* 下载地址：[https://www.virtualbox.org/wiki/Downloads][virtualbox-download]
* 推荐同时安装扩展 **VirtualBox xxxx Oracle VM VirtualBox Extension Pack**

#### 2.2. 安装 Vagrant（支持 Windows/macOS/Debian/CentOS）

* 下载地址：[https://www.vagrantup.com/downloads.html][vagrant-download]

	```bash
	# 终端下验证 Vagrant 是否安装成功
	$ vagrant --version
	Vagrant 1.9.1
	```

### 3. 配置、启动 Vagrant

#### 3.1. 增加一个 box

```
# 方式一：使用 box 的绝对地址
$ vagrant box add ubuntu1404 https://github.com/kraksoft/vagrant-box-ubuntu/releases/download/14.04/ubuntu-14.04-amd64.box

# 方式二：使用下载好的本地 box 文件（推荐此种方式，可从第三方仓库下载）
$ vagrant box add ubuntu1404 ./ubuntu-14.04-amd64.box
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'ubuntu1404' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/用户名/vagrant/boxs/ubuntu-14.04-amd64.box
==> box: Successfully added box 'ubuntu1404' (v0) for 'virtualbox'!

# 方式三：使用 Vagrant 官方仓库中对应的 box 名称
# 此种方式无法修改 box 名称，并且某些网络下访问缓慢
$ vagrant box add ubuntu/trusty64

-----------------------------------
# 查看已经添加的 box
$ vagrant box list
ubuntu1404 (virtualbox, 0)

```

#### 3.2. 初始化、启动

```
# 创建一个工作目录
$ mkdir -p ~/vagrant/lamp
$ cd ~/vagrant/lamp

# 以名字为 ubuntu1404 的 box 初始化 Vagrant
$ vagrant init ubuntu1404

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

$ ls -la
total 8
drwxr-xr-x  3 用户名  staff   102  3  2 21:13 .
drwxr-xr-x  5 用户名  staff   170  3  2 18:47 ..
-rw-r--r--  1 用户名  staff  3011  3  2 21:13 Vagrantfile

# 启动 Vagrant
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu1404'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: lamp_default_1488461535325_84495
==> default: Fixed port collision for 22 => 2222. Now on port 2201.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2201 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2201
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Remote connection disconnect. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default:
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
==> default: Mounting shared folders...
    default: /vagrant => /Users/用户名/myvagrant/lamp
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant

The error output from the command was:

mount: unknown filesystem type 'vboxsf'


# 查看当前目录下（Vagrant配置文件）对应的虚拟机的运行状态
$ vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

完成上面的步骤，其实一个由 Vagrant 管理的虚拟机已经启动起来了。此时，如果我们打开 VirtualBox 软件，在左侧的列表我们可以看到一个被新添加、并且**正在运行**状态的虚拟机。所以，并不需要打开 VirtualBox 软件，全部由 Vagrant 在命令行进行管理会更方便，参见附录部分：**5.1. 常用命令**

虽然 Vagrant 已经启动运行了，但是在启动过程报错：`mount: unknown filesystem type 'vboxsf'` 这主要是下载的 box 里面 VirtualBox 扩展有问题，需要重新处理一下，详见附录部分：**5.3. 解决 mount: unknown filesystem type 'vboxsf'**


#### 3.3. ssh 到虚拟机

```
$ vagrant ssh
Welcome to Ubuntu 14.04 LTS (GNU/Linux 3.13.0-24-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
vagrant@vagrant-ubuntu-trusty:~$ cat /etc/os-release
NAME="Ubuntu"
VERSION="14.04, Trusty Tahr"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 14.04 LTS"
VERSION_ID="14.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
```

### 4. 配置 LAMP

上一步已经启动了一个 Linux 的虚拟机，之后的环境搭建，我们就按照正常服务器搭建的流程来操作即可，这里主要介绍两种搭建 LAMP 环境的方式：

* 方式一：使用*一键LAMP&LNMP编译安装包*，请到[这里下载](/Linux/sh-1.5.5/)；
* 方式二：使用 ubuntu 的 apt-get 直接安装二进制包，如下：

	```
	# 安装 MySQL
	$ sudo apt-get install mysql-server mysql-client
	
	# 安装 Apache
	$ sudo apt-get install apache2
	
	# 安装 PHP
	$ sudo add-apt-repository ppa:ondrej/php
	$ sudo apt-get update
	$ sudo apt-get install php5.6
	
	# 安装 PHP 扩展
	$ sudo apt-get install libapache2-mod-php5.6 php5.6-mysql php5.6-gd php5.6-curl php5.6-dev php5.6-xml php5.6-mbstring
	
	-----------------------------------
	# 测试
	$ sudo vim /var/www/html/info.php
	<?php phpinfo();
	
	$ curl localhost 或从宿主机访问
	
	```

### 5. 附录

#### 5.1. 常用命令

```
# 添加 box
$ vagrant box add new-box-name box文件地址（本地、远程）

# 删除 box
$ vagrant box remove box-name

# 以指定的 box 初始化
$ vagrant init new-box-name

# 启动
$ vagrant up

# 关闭
$ vagrant halt

# 重启，重新加载配置文件
$ vagrant reload

# 挂起
$ vagrant suspend

# 关闭、删除 Vagrant 创建的虚拟机资源
$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...

# 打包
$ vagrant package --output 自定义的包名.box

# 更多详细说明
$ vagrant -h
$ vagrant 命令名 -h

```

#### 5.2. Vagrantfile 常用配置

```
# 虚拟机的 hostname
config.vm.hostname = "ubuntu1404-lamp”

# 网络设置，一般设置私有（private_network）网络，并结合端口映射
config.vm.network "private_network", ip: "192.168.47.10"
config.vm.network "forwarded_port", guest: 80, host: 8080

# 共享目录
# 注意：这里的 owner 和 group，与你搭建的 LAMP 环境运行用户一致（在 phpinfo() 页面中搜索 “user”）
config.vm.synced_folder "/Users/用户名/www", "/var/www/html", create:true, owner:"www-data", group:"www-data"

# VirtualBox 虚拟机配置（内存、CPU、显示名称等）
config.vm.provider "virtualbox" do |vb|
#   # Display the VirtualBox GUI when booting the machine
#   vb.gui = true
#
#   # Customize the amount of memory on the VM:
    vb.memory = "1024"
    vb.name = "ubuntu1404-lamp”
end
```

#### 5.3. 解决 mount: unknown filesystem type 'vboxsf'

+ 关闭虚拟机 `$ vagrant halt`
+ 使用 VirtualBox 启动虚拟机
+ 添加虚拟机增强工具：依次单击菜单【设备】→【安装增强功能】
+ 在 VirtualBox 中登录虚拟机，并执行以下命令挂载、安装

    ```
    sudo mount /dev/cdrom /media/cdrom
    cd /media/cdrom/
    sudo ./VBoxLinuxAddtions.run
    ```

+ 安装成功后，重新启动 `$ vagrant up`

#### 5.4. 关闭静态文件缓存

使用 Apache/Nginx 时会出现诸如图片修改后但页面刷新仍然是旧文件的情况，是由于静态文件缓存造成的。需要对虚拟机里的 Apache/Nginx 配置文件进行修改：

```
# Apache 配置（httpd.conf 或者 apache.conf）添加：
EnableSendfile off

# Nginx 配置（nginx.conf）添加：
sendfile off;
```
