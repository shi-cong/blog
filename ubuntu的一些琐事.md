# ubuntu的一些琐事
由于本人的所有的中间件（mysql,redis,mongdb,postgresql,amqp,nginx,python)都是运行在ubuntu下的，所以就会
经常遇到一些需求。

## 如何修改ubuntu的主机名？
查看主机名
```bash
hostname
```
临时修改主机名
```bash
hostname [新主机名]
#这样主机名字就临时被修改为[新主机名]，但是终端下不会立即显示生效后的主机名，重开一个终端窗口(通过ssh连接的终端需要重新连接才可以);
```
永久修改主机名

	在Ubuntu系统中永久修改主机名也比较简单。主机名存放在/etc/hostname文件中，修改主机名时，编辑hostname文件，在文件中输入新的主机名并保存该文件即可。重启系统后，参照上面介绍的快速查看主机名的办法来确认主机名有没有修改成功。

	值的指出的是，在其它Linux发行版中，并非都存在/etc/hostname文件。如Fedora发行版将主机名存放在/etc/sysconfig/network文件中。所以，修改主机名时应注意区分是哪种Linux发行版。

	3、/etc/hostname与/etc/hosts的区别
	/etc/hostname中存放的是主机名，hostname文件的一个例子：
	v-jiwan-ubuntu-temp

	/etc/hosts存放的是域名与ip的对应关系，域名与主机名没有任何关系，你可以为任何一个IP指定任意一个名字，hostname文件的一个例子：
	127.0.0.1       localhost
	127.0.1.1       v-jiwan-ubuntu

操作完毕之后记得重启！

## 如何删除用户并创建新用户？
创建新用户
```bash
adduser python3_1
```
将用户增加到 sudoers file文件
```bash
vi /etc/sudoers
# 增加一行
# 下面的操作是将新增的用户增加sudo权限，然后删除默认创建的用户
python3_1 ALL=(ALL) ALL
# 保存
reboot
# 重启之后再以默认创建的用户登陆再到root用户权限，然后删除默认用户
deluser nginx_miku
```
操作完毕之后记得重启！

## 部署python3项目
由于本人的所有的python库都是基于自己的PYSTUDY，所以，很大程度上，做api接口程序只用最好的，那么就是tornado，
本人的数据库连接池也是自己写的，基本上不依赖于第三方的库，基本上，做开发的东西除了我的数据库连接池有点不靠谱，当然这个在我的爬虫系统中没有太大的问题，其它商业软件涉及支付的地方，我可能不会用自己设计的东西，到了商业
软件的时候，我可能还是会走一些django这种大众的项目作为商业软件开发的基础。因为本人写的一些东西还没实际在生产环境使用过，所以，没有那个胆量。

再者，我是极其讨厌centos系统的，所以本人的所有linux系统都是基于ubuntu的。由于在centos曾经有过惨痛经历，所以我选择了ubuntu。
```bash
apt update
apt install python3-pip --fix-missing -y
apt install python-dev libreadline-dev libssl-dev openssl libxml2 libxml2-dev -y
pip3 install PYSTUDY
```
如果遇到了scrapy安装错误，先apt clean然后安装gcc.

遇到了一个问题，不准备重装linux系统，这估计是官方的bug。

## 如何查看ubuntu版本？
* `cat /etc/issue`
* `sudo lsb_release -a`

## 如何更改ubuntu的默认源？
```
vi /etc/apt/source.list
```

做运维真的很浪费时间。但是tmd连个软件的运行环境都搞不好，还想搞好编程？

## ubuntu.16 和 ubuntu 17配置ip和网络管理上有很大的区别
	ubuntu 17
	系统安装完成之后默认是动态分配IP

	liyaoyi@ubuntu:~$ ifconfig
	ens32: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
		inet 192.168.209.221  netmask 255.255.255.0  broadcast 192.168.209.255
		inet6 fe80::20c:29ff:fe9b:2437  prefixlen 64  scopeid 0x20<link>
		ether 00:0c:29:9b:24:37  txqueuelen 1000  (Ethernet)
		RX packets 632  bytes 78029 (78.0 KB)
		RX errors 0  dropped 0  overruns 0  frame 0
		TX packets 440  bytes 56139 (56.1 KB)
		TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

	lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
		inet 127.0.0.1  netmask 255.0.0.0
		inet6 ::1  prefixlen 128  scopeid 0x10<host>
		loop  txqueuelen 1000  (Local Loopback)
		RX packets 102  bytes 7728 (7.7 KB)
		RX errors 0  dropped 0  overruns 0  frame 0
		TX packets 102  bytes 7728 (7.7 KB)
		TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


	之前的版本是在/etc/network/interfaces中修改,新版本使用/etc/netplan/*.yaml

	liyaoyi@ubuntu:~$ cat /etc/netplan/01-netcfg.yaml
	# This file describes the network interfaces available on your system
	# For more information, see netplan(5).
	network:
	  version: 2
	  renderer: networkd
	  ethernets:
	    ens32:
	      dhcp4: yes
	编辑YAML文件配置IP信息

	liyaoyi@ubuntu:~$ sudo vim /etc/netplan/01-netcfg.yaml
	# This file describes the network interfaces available on your system
	# For more information, see netplan(5).
	network:
	  version: 2
	  renderer: networkd
	  ethernets:
	    ens32:
	      dhcp4: no
	      dhcp6: no
	      addresses: [192.168.209.221/24]
	      gateway4: 192.168.209.2
	      nameservers:
		addresses: [8.8.8.8, 114.114.114.114]


	执行命令让配置生效：

	liyaoyi@ubuntu:~$ sudo netplan apply


	显示debug信息

	liyaoyi@ubuntu:~$ sudo netplan  --debug  apply
	** (generate:2842): DEBUG: Processing input file //etc/netplan/01-netcfg.yaml..
	** (generate:2842): DEBUG: starting new processing pass
	** (generate:2842): DEBUG: ens32: setting default backend to 1
	** (generate:2842): DEBUG: Generating output files..
	** (generate:2842): DEBUG: NetworkManager: definition ens32 is not for us (backend 1)
	DEBUG:netplan generated networkd configuration exists, restarting networkd
	DEBUG:no netplan generated NM configuration exists
	DEBUG:device ens32 operstate is up, not replugging
	DEBUG:netplan triggering .link rules for ens32
	DEBUG:device lo operstate is unknown, not replugging
	DEBUG:netplan triggering .link rules for lo


	netplan 其他参数指令：

	netplan generate: 执行后使用 /etc/netplan 生成/run/systemd/network/10-netplan-ens32.network



	netplan ifupdown-migrate: 尝试从/etc/network/interfaces转换成netplan需要的yaml格式,如果转换成功会禁止使用/etc/network/interfaces

ubuntu 16
	1、vi /etc/network/interfaces
	添加内容：
	auto eth0
	iface eth0 inet static
	address 192.168.8.100    
	netmask 255.255.255.0
	gateway 192.168.8.2
	dns-nameserver 119.29.29.29

	dns-nameserver 119.29.29.29这句一定需要有，
	因为以前是DHCP解析，所以会自动分配DNS 服务器地址。
	而一旦设置为静态IP后就没有自动获取到DNS服务器了，需要自己设置一个
	设置完重启电脑后，/etc/resolv.conf 文件中会自动添加 nameserver 119.29.29.29
	(或者nameserver 8.8.8.8)可以根据访问速度，选择合适的公共DNS 



	2、重启网络：sudo /etc/init.d/networking restart
