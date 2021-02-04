
![Vagrant](./vagrant.png)

Vagrant 入门指引
---

### 目录

1. [Vagrant 介绍](#1-vagrant-介绍)
2. [安装 Vagrant 和 VirtualBox](#2-安装-vagrant-和-virtualbox)
	- 2.1. [安装 VirtualBox（支持 Windows/macOS/Linux）](#21-安装-virtualbox支持-windowsmacoslinux)
	- 2.2. [安装 Vagrant（支持 Windows/macOS/Debian/CentOS）](#22-安装-vagrant支持-windowsmacosdebiancentos)
3. [配置、启动 Vagrant](#3-配置启动-vagrant)
	- 3.1. [添加一个 box](#31-添加一个-box)
	- 3.2. [初始化、启动](#32-初始化启动)
	- 3.3. [ssh 到虚拟机](#33-ssh-到虚拟机)
4. [常用命令](#4-常用命令)
5. [附录](#5-附录)
	- 5.1. [Vagrantfile 常用配置](#51-vagrantfile-常用配置)
	- 5.2. [关闭静态文件缓存](#52-关闭静态文件缓存)
	- 5.3. [虚拟机与宿主机时间同步设置](#53-虚拟机与宿主机时间同步设置)
	- 5.4. [安装 VirtualBox 扩展工具](#54-安装-virtualbox-扩展工具)
6. [常见问题处理](#6-常见问题处理)
	- 6.1. [mount: unknown filesystem type 'vboxsf'](#61-mount-unknown-filesystem-type-vboxsf)
	- 6.2. [vagrant@192.168.127.11: Permission denied (publickey,gssapi-keyex,gssapi-with-mic)](#62-vagrant19216812711-permission-denied-publickeygssapi-keyexgssapi-with-mic)
	- 6.3. [/sbin/mount.vboxsf: mounting failed with the error: No such device](#63-sbinmountvboxsf-mounting-failed-with-the-error-no-such-device)

[vagrant-homepage]: https://www.vagrantup.com "Vagrant homepage"
[vagrant-docs]: https://www.vagrantup.com/docs "Vagrant docs"
[vagrant-box]: https://app.vagrantup.com/boxes/search "Vagrant box"
[vagrant-box-thd]: http://www.vagrantbox.es "Vagrant box"
[vagrant-box-thd-tsinghua]: https://mirrors.tuna.tsinghua.edu.cn/ubuntu-cloud-images "Vagrant box"
[vagrant-box-thd-ustc]: https://mirrors.ustc.edu.cn/ubuntu-cloud-images/ "Vagrant box"
[vagrant-docker]: https://www.zhihu.com/question/32324376 "Vagrant Docker"
[virtualbox-download]: https://www.virtualbox.org/wiki/Downloads "Virtualbox Download"
[vagrant-download]: https://www.vagrantup.com/downloads.html "Vagrant Download"

---

### 1. Vagrant 介绍

相关资源

* 官网：[https://www.vagrantup.com][vagrant-homepage]
* 文档（英文）：[https://www.vagrantup.com/docs][vagrant-docs]
* 文档（中文）：[https://tangbaoping.github.io/vagrant_doc_zh/v2](https://tangbaoping.github.io/vagrant_doc_zh/v2)
* 官方 box 仓库：[https://app.vagrantup.com/boxes/search][vagrant-box]
* 第三方 box 仓库：[http://www.vagrantbox.es][vagrant-box-thd]（还有[清华][vagrant-box-thd-tsinghua]、[中科大 👍][vagrant-box-thd-ustc]）
* CentOS 官方 box 地址：[http://cloud.centos.org/centos/7/vagrant/x86_64/images/](http://cloud.centos.org/centos/7/vagrant/x86_64/images/)
* Ubuntu 官方 box 地址：[http://cloud-images.ubuntu.com](http://cloud-images.ubuntu.com)

下面是节选自官方对 Vagrant 的说明：

> **WHY VAGRANT?**
>
> Vagrant provides easy to configure, reproducible, and portable work environments built on top of industry-standard technology and controlled by a single consistent workflow to help maximize the productivity and flexibility of you and your team.
>
> To achieve its magic, Vagrant stands on the
oulders of giants. Machines are provisioned on top of **VirtualBox**, **VMware**, **AWS**, or any other provider. Then, industry-standard provisioning tools such as shell scripts, Chef, or Puppet, can be used to automatically install and configure software on the machine.

其实，说白了 Vagrant 就是一个普普通通的装了 Linux 的 VirtualBox 虚拟机，配以 Vagrant  团队为之开发的一系列套件，辅助完成诸如安装初始化、文件同步、ssh、部署环境升级、功能插件安装等等一些列问题的开发环境部署套件。

参见知乎话题：[Vagrant 和 Docker的使用场景和区别?][vagrant-docker]

**解决的痛点**：

* 开发环境快速部署
* 开发环境更迭

### 2. 安装 Vagrant 和 VirtualBox

> 官方说明中，Vagrant 是支持 VirtualBox/VMware/AWS 等虚拟软件的，选择 VirtualBox 主要是因为开源、免费、轻便。


#### 2.1. 安装 VirtualBox

VirtualBox 支持 Windows、macOS、Linux 等系统。

下载地址：[https://www.virtualbox.org/wiki/Downloads][virtualbox-download]

建议同时安装扩展 **VirtualBox xxxx Oracle VM VirtualBox Extension Pack**

#### 2.2. 安装 Vagrant

Vagrant 支持 Windows、CentOS、Linux x64、macOS 等系统。

下载地址：[https://www.vagrantup.com/downloads.html][vagrant-download]

安装包是二进制文件，根据提示一步一步安装即可。安装程序会将 `vagrant` 命令添加到系统环境（path）变量，安装结束后可通过下面到命令检查：

```bash
# 在终端下验证 Vagrant 是否安装成功
$ vagrant --version
Vagrant 2.2.4

# 或者执行以下命令
# 该命令在查看软件版本的同时，会检测是否有新版本可更新
$ vagrant version
Installed Version: 2.2.4
Latest Version: 2.2.6

You're running an up-to-date version of Vagrant!
```

> ⚠️ 关于软件升级：
>
> 1. 不要单独升级 VirtualBox 或 Vagrant ，二者需要版本匹配，如果你的 VirtualBox 升级了最新版本，那么一定要检查 Vagrant 是否也做了更新。
> 2. 升级 VirtualBox 时，需同时升级扩展。
> 3. 升级 Vagrant 时候，推荐先卸载，再安装最新版本。

### 3. 配置、启动 Vagrant

#### 3.1. 添加一个 box

从头开始创建一个虚拟机，是一个漫长而乏味的过程，因此 Vagrant 是通过基础镜像包来实现快速克隆创建虚拟机的。这些基础镜像包在 Vagrant 中被称为 `boxes` ， 而在创建 `Vagrantfile` 文件后的第一件事情就是指定 Vagrant 环境使用哪一个 Box。

原始的 box 是一个包含了基本系统和设置的镜像包，你可以通过基础包安装软件或做一些自定义配置，然后导出来成为新的基础包，再次使用的时候，直接导入你之前导出的这个 box 即可。

如何添加？

- 方式一：使用 [Vagrant 官方仓库][vagrant-box] 中对应的 box 名称

	```bash
	# 此种方式无法修改添加的 box 名称
	# 并且在某些情况下，下载速度可能比较缓慢，所以推荐使用方式二
	$ vagrant box add ubuntu/xenial64
	==> box: Loading metadata for box 'ubuntu/xenial64'
			box: URL: https://vagrantcloud.com/ubuntu/xenial64
	==> box: Adding box 'ubuntu/xenial64' (v20190807.0.0) for provider: virtualbox
			box: Downloading: https://vagrantcloud.com/ubuntu/boxes/xenial64/versions/20190807.0.0/providers/virtualbox.box
			box: Download redirected to host: cloud-images.ubuntu.com
	==> box: Successfully added box 'ubuntu/xenial64' (v20190807.0.0) for 'virtualbox'!
	```

- 方式二：使用 [第三方仓库][vagrant-box-thd]（还有[清华][vagrant-box-thd-tsinghua]、[中科大 👍][vagrant-box-thd-ustc]），速度相对快一些，也更灵活

	```bash
	# 使用 box 的绝对路径
	$ vagrant box add centos7 https://github.com/holms/vagrant-centos7-box/releases/download/7.1.1503.001/CentOS-7.1.1503-x86_64-netboot.box
	==> box: Box file was not detected as metadata. Adding it directly...
	==> box: Adding box 'centos7' (v0) for provider:
			box: Downloading: https://github.com/holms/vagrant-centos7-box/releases/download/7.1.1503.001/CentOS-7.1.1503-x86_64-netboot.box
			box: Download redirected to host: github-production-release-asset-2e65be.s3.amazonaws.com
	==> box: Successfully added box 'centos7' (v0) for 'virtualbox'!

	# 使用之前导出的 box 或离线下载好的 box
	# 导出方式详见下文的常用命令
	$ vagrant box add ubuntu1604 ./boxs/xenial-server-cloudimg-amd64-vagrant.box
	==> box: Box file was not detected as metadata. Adding it directly...
	==> box: Adding box 'ubuntu1604' (v0) for provider:
	    box: Unpacking necessary files from: file:///Users/sunqiang/myvagrant/boxs/xenial-server-cloudimg-amd64-vagrant.box
	==> box: Successfully added box 'ubuntu1604' (v0) for 'virtualbox'!
	```

检查 box 是否添加成功

```bash
$ vagrant box list
centos7         (virtualbox, 0)
ubuntu/xenial64 (virtualbox, 20190807.0.0)
ubuntu1604      (virtualbox, 0)
```

#### 3.2. 初始化、启动

1. 创建一个工作目录

	```bash
	$ mkdir -p ~/vagrant/lamp
	$ cd ~/vagrant/lamp
	```

2. 以名字为 *ubuntu/xeninal64* 的 box 初始化 Vagrant

	```bash
	$ vagrant init ubuntu/xeninal64


	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.

	$ ls -la
	total 8
	drwxr-xr-x  3 用户名  staff   102  3  2 21:13 .
	drwxr-xr-x  5 用户名  staff   170  3  2 18:47 ..
	-rw-r--r--  1 用户名  staff  3011  3  2 21:13 Vagrantfile
	```

3. 启动 Vagrant

	```bash
	$ vagrant up
	Bringing machine 'default' up with 'virtualbox' provider...
	==> default: Importing base box 'ubuntu/xenial64'...
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
	```

4. 查看当前目录下（Vagrant配置文件）对应的虚拟机的运行状态

	```bash
	$ vagrant status
	Current machine states:

	default                   running (virtualbox)

	The VM is running. To stop this VM, you can run `vagrant halt` to
	shut it down forcefully, or you can run `vagrant suspend` to simply
	suspend the virtual machine. In either case, to restart it again,
	simply run `vagrant up`.
	```

完成上面的步骤，其实一个由 Vagrant 管理的虚拟机已经启动起来了。

此时，如果我们打开 VirtualBox 软件，在左侧的列表我们可以看到一个被新添加、并且**正在运行**状态的虚拟机。所以，并不需要打开 VirtualBox 软件，全部由 Vagrant 在命令行进行管理会更方便，参见附录部分：[4. 常用命令](#4-常用命令)

> ⚠️ **注意**：
>
> 虽然 Vagrant 已经启动运行了，但是在启动过程可能会报错（一般存在于使用第三方下载的 box 时）：
>
> `mount: unknown filesystem type 'vboxsf'`
>
> 这主要是下载的 box 里面 VirtualBox 扩展有问题，需要重新处理一下，参见常见问题部分：[6.1. mount: unknown filesystem type 'vboxsf'](#61-mount-unknown-filesystem-type-vboxsf)

#### 3.3. ssh 到虚拟机

在工作目录下使用如下命令 ssh 登录到正在运行的虚拟机

```bash
$ vagrant ssh
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


_____________________________________________________________________
WARNING! Your environment specifies an invalid locale.
 The unknown environment variables are:
   LC_CTYPE=zh_CN.UTF-8 LC_ALL=
 This can affect your user experience significantly, including the
 ability to manage packages. You may install the locales by running:

   sudo apt-get install language-pack-zh
     or
   sudo locale-gen zh_CN.UTF-8

To see all available language packs, run:
   apt-cache search "^language-pack-[a-z][a-z]$"
To disable this message for all users, run:
   sudo touch /var/lib/cloud/instance/locale-check.skip
_____________________________________________________________________
ubuntu@ubuntu-xenial:~$ sudo locale-gen zh_CN.UTF-8
Generating locales (this might take a while)...
  zh_CN.UTF-8... done
Generation complete.
ubuntu@ubuntu-xenial:~$ cat /etc/os-release
NAME="Ubuntu"
VERSION="16.04.3 LTS (Xenial Xerus)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 16.04.3 LTS"
VERSION_ID="16.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
VERSION_CODENAME=xenial
UBUNTU_CODENAME=xenial
ubuntu@ubuntu-xenial:~$ exit
logout
Connection to 127.0.0.1 closed.
```

> root 默认密码，通常为 vagrant

### 4. 常用命令

```bash
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

# 创建快照（vm 名称使用 vagrant status 查看）
$ vagrant snapshot save [vm名称] [快照名称]

# 查看快照列表
$ vagrant snapshot list

# 还原到指定快照
$ vagrant snapshot restore [vm名称] [快照名称]

# 删除快照
$ vagrant snapshot delete [快照名称]

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

### 5. 附录

#### 5.1. Vagrantfile 常用配置

```bash
# 虚拟机中 linux 的 hostname
config.vm.hostname = "vm-ubuntu1604-localhost"

# 虚拟机名称，在类似创建备份等操作中会用到
config.vm.define "vm-ubuntu1604"

# 网络设置1 - 公有（public_network）网络（允许局域网中其他机器访问）
config.vm.network "public_network", ip: "192.168.1.223"

# 网络设置2 - 私有（private_network）网络（只允许主机访问虚拟机）
# 此时我们可以使用指定的 IP 加 端口号进行访问，比如使用 192.168.127.11:81 即可访问虚拟机里的 81 端口
config.vm.network "private_network", ip: "192.168.127.11"

# 网络设置3 - 将端口映射到宿主机
# 在宿主机使用 127.0.0.1:8080 即可访问虚拟机里的 80 端口
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
    vb.memory = "512"
    vb.name = "ubuntu1604"
end
```

#### 5.2. 关闭静态文件缓存

使用 Apache/Nginx 时会出现诸如图片修改后但页面刷新仍然是旧文件的情况，是由于静态文件缓存造成的。需要对虚拟机里的 Apache/Nginx 配置文件进行修改：

```bash
# Apache 配置（httpd.conf 或者 apache.conf）添加：
EnableSendfile off

# Nginx 配置（nginx.conf）添加：
sendfile off;
```

#### 5.3. 虚拟机与宿主机时间同步设置

```bash
#
$ VBoxManage list vms
"win7" {87fd57f0-ef1f-4e6d-8529-0fee472ba76a}
"win10" {a7edfd3b-0706-43f6-bd3d-b79dc796eb28}
"ubuntu1604" {72329a00-87e3-40be-b5fc-a4417ce2aecb}

#
$ VBoxManage getextradata "ubuntu1604" "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled"
No value set!

#
$ VBoxManage setextradata "ubuntu1604" "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled" 0

# 已经设置成功
$ VBoxManage getextradata "ubuntu1604" "VBoxInternal/Devices/VMMDev/0/Config/GetHostTimeDisabled"
Value: 0

# 重新启动虚拟机以加载修改的配置
$ vagrant reload
```


#### 5.4. 安装 VirtualBox 扩展工具

如果使用官方 box 初始化虚拟机，其实得到的是一个纯净的镜像，一些基础工具和设置需要你自己来配置（所以，还是推荐使用方式二）。

比如想要实现文件夹共享、网络、端口映射等，必须要安装 VirtualBox 扩展工具，具体安装方式如下：

```bash
# 进入虚拟机
$ vagrant ssh

# 安装所需开发工具
ubuntu@ubuntu1604:~$ sudo apt-get install linux-headers-$(uname -r) build-essential dkms

# VBoxGuestAdditions 扩展的版本，取前三位，如：5.2.20
# 所有可用版本：http://download.virtualbox.org/virtualbox
ubuntu@ubuntu1604:~$  cd /tmp
ubuntu@ubuntu1604:~$  wget http://download.virtualbox.org/virtualbox/5.2.20/VBoxGuestAdditions_5.2.20.iso
ubuntu@ubuntu1604:~$  sudo mkdir /media/VBoxGuestAdditions
ubuntu@ubuntu1604:~$  sudo mount -o loop,ro VBoxGuestAdditions_5.2.20.iso /media/VBoxGuestAdditions
ubuntu@ubuntu1604:~$  sudo sh /media/VBoxGuestAdditions/VBoxLinuxAdditions.run
ubuntu@ubuntu1604:~$  rm VBoxGuestAdditions_5.2.20.iso
ubuntu@ubuntu1604:~$  sudo umount /media/VBoxGuestAdditions
ubuntu@ubuntu1604:~$  sudo rmdir /media/VBoxGuestAdditions
ubuntu@ubuntu1604:~$ exit

# 重启虚拟机
$ vagrant reload
```

参照：[https://www.vagrantup.com/docs/virtualbox/boxes.html](https://www.vagrantup.com/docs/virtualbox/boxes.html)

**自动检查同步新版本**

```bash
$ vagrant plugin install vagrant-vbguest
# 指定一个版本安装
$ vagrant plugin install vagrant-vbguest --plugin-version 0.21
```

之后，每次 `vagrant up` 过程中，如果发现虚拟机的 VBoxGuestAdditions 与宿主机不一致，则进行更新；如果之前没安装，则会自动安装。


### 6. 常见问题处理

#### 6.1. `mount: unknown filesystem type 'vboxsf'`

- 关闭虚拟机 `$ vagrant halt`
- 使用 VirtualBox 启动虚拟机
- 添加虚拟机增强工具：依次单击菜单【设备】→【安装增强功能】
- 在 VirtualBox 中登录虚拟机，并执行以下命令挂载、安装

    ```bash
    $ sudo mount /dev/cdrom /media/cdrom
    $ cd /media/cdrom/
    $ sudo ./VBoxLinuxAddtions.run
    ```

- 安装成功后，重新启动 `$ vagrant up`

#### 6.2. `vagrant@192.168.127.11: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).`

SSH默认禁用密码连接，只允许使用密钥登录，需修改文件 `/etc/ssh/sshd_config` 中如下部分：

```
PasswordAuthentication yes
```

#### 6.3. `/sbin/mount.vboxsf: mounting failed with the error: No such device`

```
yum clean all
yum update
yum install -y kernel kernel-devel kernel-headers gcc make
reboot
```

```
cd /opt/VBoxGuestAdditions-*/init
./vboxadd setup
reboot
```

```
vagrant reload
```
