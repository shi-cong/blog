# mac下最大连接数设置
由于最近在做并发测试，出现了too many open files的错误。所以想到了更改操作系统连接数。

## 以下是搜索来自csdn博客
Mac的最大连接数限制和端口的相关参数的设置
最大连接数限制
最大连接数限制就是系统所能打开的最大文件数（文件描述符）的限制，分全局和进程两种，相应的命令如下：

$ sysctl kern.maxfiles
输出：kern.maxfiles: 12288
说明：全局限制，也就是系统默认的最大连接数限制是12288

$ sysctl kern.maxfilesperproc
输出：kern.maxfilesperproc: 10240 
说明：单个进程默认最大连接数限制是10240

$ sudo sysctl -w kern.maxfiles=1048600
输出：kern.maxfiles: 12288 -> 1048600
说明：设置系统最大连接数从12288到1048600

$ sudo sysctl -w kern.maxfilesperproc=1048576
输出：kern.maxfilesperproc: 10240 -> 1048576
说明：设置进程连接数限制，进程的最大连接数要小于等于全局连接数

ulimit命令
$ ulimit -n
输出：2560 
说明：“ulimit －n”命令显示当前shell能打开的最大文件数，默认值：2560，该值总是小于kern.maxfilesperproc的值，因为一个shell就是一个进程。

 $ ulimit -n 1048576
说明：设置当前shell能打开的最大文件数为1048576，该值不能大于kern.maxfilesperproc，否则会提示设置失败。

动态端口范围
$ sysctl net.inet.ip.portrange
输出： 
net.inet.ip.portrange.first: 49152 
net.inet.ip.portrange.last: 65535

Linux动态端口号默认范围是32768-65535，也就是说，作为客户端连接同一个IP和同一个端口号，最多只能建立30000多个连接，而Mac默认只能建立16000个左右的连接。

$ sysctl -w net.inet.ip.portrange.first=32768
说明：设置动态分配起点端口号为32768，这样可以增大客户端可以建立的连接数。

参考： The IANA list of port numbers includes the well-known and registered port numbers and specifies the dynamic port number range.

问题
按以上的方式设置参数有个问题，当系统重启后，这些参数又恢复成了默认值，解决办法就是把参数写到/etc/sysctl.conf文件中，但是，默认这个文件是不存在的，所以首先就要创建它：

sudo touch /etc/sysctl.conf
然后把参数写到文件里

kern.maxfiles=1048600
kern.maxfilesperproc=1048576
net.inet.ip.portrange.first=49152   
net.inet.ip.portrange.last=65535
重启系统，查看结果，显示成功。

至于ulimit-n的值，可以把ulimit－n 1048576 写到.bashrc中实现自动修改。


