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
