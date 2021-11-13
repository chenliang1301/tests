# 第1课第2.3.1节_数码相框_freetype理论介绍

1. 程序框架
1.1 触摸屏:
              主按线程,通过socket发给显示进程
              ---------------------------
              封装事件：ts线程          按键线程
              ---------------------------
                       操作系统                        

封装的数据有:
时间
类型(点击、上下左右移动)
位置
速度
幅度

1.2 显示

放大(上)  缩小(下)  左边    右边   当前    显示控制     接收sochket

             libjpeg
              mmap
----------------------------------------
内存      内存      内存    内存    内存

                                            framebuffer
                                            -----------
                                                 LCD

2. 显示文字
2.1 文字编码方式
源文件用不同的编码方式编写，会导致执行结果不一样。
怎么解决？编译程序时，要指定字符集
man gcc , /charset
-finput-charset=charset  表示源文件的编码方式, 默认以UTF-8来解析
-fexec-charset=charset   表示可执行程序里的字时候以什么编码方式来表示，默认是UTF-8

gcc -o a a.c  //

gcc -finput-charset=GBK -fexec-charset=UTF-8 -o utf-8_2 ansi.c

2.2 英文字母、汉字的点阵显示
测试:
A. 配置、修改内核支持把lcd.c编译进去
cp /work/drivers_and_test_new/10th_lcd/lcd.c drivers/video/
修改drivers/video/Makefile
#obj-$(CONFIG_FB_S3C2410)         += s3c2410fb.o
obj-$(CONFIG_FB_S3C2410)          += lcd.o

nfs 32000000 192.168.1.123:/work/nfs_root/uImage; bootm 32000000

set bootargs console=ttySAC0,115200 root=/dev/nfs nfsroot=192.168.1.123:/work/nfs_root/fs_mini_mdev_new ip=192.168.1.17
nfs 32000000 192.168.1.123:/work/nfs_root/uImage; bootm 32000000
nfs 32000000 192.168.1.123:/work/nfs_root/uImage_tq2440; bootm 32000000
nfs 32000000 192.168.1.123:/work/nfs_root/uImage_mini2440; bootm 32000000

B. 使用新内核启动

2.3 使用freetype来显示任意大小的文字
2.3.1节_数码相框_freetype理论介绍
2.3.2节_数码相框_在PC上测试freetype

在PC：
tar xjf freetype-2.4.10.tar.bz2 
./configure
make
sudo make install

gcc -o example1 example1.c  -I /usr/local/include/freetype2 -lfreetype -lm
gcc -finput-charset=GBK -fexec-charset=UTF-8 -o example1 example1.c  -I /usr/local/include/freetype2 -lfreetype -lm
./example1 ./simsun.ttc abc

2.3.3节_数码相框_在LCD上显示一个矢量字体

交叉编译:
tar xjf freetype-2.4.10.tar.bz2 
./configure --host=arm-linux   (配置他，交叉编译)
make
make DESTDIR=$PWD/tmp install  （安装到当前目录下的tmp中）

将头文件和库分别拷贝到交叉工具链里面起

编译出来的头文件应该放入：
/usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/usr/include

编译出来的库文件应该放入：
/usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/armv4t/lib

把tmp/usr/local/lib/*  复制到 /usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/armv4t/lib
sudo cp * /usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/armv4t/lib -d -rf
cp *so* /work/nfs_root/fs_mini_mdev_new/lib -d

把tmp/usr/local/include/*  复制到 /usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/usr/include
cp * /usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/usr/include -rf
cd /usr/local/arm/4.3.2/arm-none-linux-gnueabi/libc/usr/include
mv freetype2/freetype .  （代码中只用到freetype而没有用到freetype2，因此将他移出）

arm-linux-gcc -finput-charset=GBK -o example1 example1.c  -lfreetype -lm
arm-linux-gcc -finput-charset=GBK -o show_font show_font.c  -lfreetype -lm

freetype/config/ftheader.h
freetype2/freetype/config/ftheader.h 

arm-linux-gcc -finput-charset=GBK -fexec-charset=GBK -o show_font show_font.c -lfreetype -lm 

2.3.4节_数码相框_在LCD上显示几行文字
a. 从左边开始显示几行文字
arm-linux-gcc -finput-charset=GBK -o show_lines show_lines.c  -lfreetype -lm

b. 居中显示几行文字

百问网gif
www.100ask.net

2.4 编写一个通用的Makefile

gcc -o example1 example1.c -L/usr/local/lib/ -lfreetype -lm -I /usr/local/include/freetype2 
gcc -finput-charset=GBK -fexec-charset=UTF-8 -o example1 example1.c -L/usr/local/lib/ -lfreetype -lm -I /usr/local/include/freetype2 
gcc -finput-charset=UTF-8 -fexec-charset=UTF-8 -o example1 example1.c -L/usr/local/lib/ -lfreetype -lm -I /usr/local/include/freetype2 

参考资料：
FreeType 字体引擎分析与指南
http://wenku.baidu.com/view/2d24be10cc7931b765ce155b.html

HZK16应用示例
http://wenku.baidu.com/view/0774d20c52ea551810a68768.html

点阵字库HZK12 HZK16 HZK24 ASC12 ASC16 简介 及 使用方法 
http://blog.csdn.net/hongjiujing/article/details/6649618

汉字拼音、五笔、GB2312、GBK、Unicode、BIG5编码速查
http://ipseeker.cn/tools/pywb.php

在线汉字编码查询,一次查询多个汉字输入法编码及内码——快典网.htm
http://bm.kdd.cc/

BIG5编码表
http://wenku.baidu.com/view/9bb3ae01b52acfc789ebc970.html

UNICODE编码表
http://wenku.baidu.com/view/7c667f563c1ec5da50e27069.html

GB2312简体中文编码表
http://wenku.baidu.com/view/0ef57bfb04a1b0717fd5dd1a.html

hzk16的介绍以及简单的使用方法
http://hi.baidu.com/hrman/blog/item/4616bc2675ce13128a82a193.html

gcc -o example1 example1.c -I /usr/local/include/freetype2 -lfreetype -lm