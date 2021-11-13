# 挂接nfs网络文件

mount -t nfs -o nolock,vers=2 192.168.1.102:/work/nfs_root /mnt
开发板与有线网卡直连

设置主机ip地址

移植完linux-3.4.2内核，启动系统后使用命令ifconfig -a查看网络配置，没有eth0
原创 Alen.Wang 最后发布于2017-03-02 20:04:26 阅读数 1921 收藏
展开
问题：
/ # ifconfig
/ # ifconfig eth0 
ifconfig: eth0: error fetching interface information: Device not found
/ # ifconfig eth0 up
ifconfig: SIOCGIFFLAGS: No such device

原因：机器id如果是SMDK2440的16a就会出现上述问题。 
解决办法：在uboot界面设置机器id为MINI2440的7CF。

在UBOOT里：
set machid 7CF   // mini2440  mach-mini2440.c
————————————————
版权声明：本文为CSDN博主「Alen.Wang」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_26093511/article/details/59642827

sudo vi /etc/hosts

![%E6%8C%82%E6%8E%A5nfs%E7%BD%91%E7%BB%9C%E6%96%87%E4%BB%B6%2011d6e2677ade416996fba7bf89b821fd/Untitled.png](D:\08 git\notion\all\韦东山\第三期\数码相框\images\Untitled-1636554726572.png)

nfs 32000000 192.168.1.102:/work/nfs_root/uImage; bootm 32000000