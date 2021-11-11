# 第005课_linux进阶命令

### linux进阶命令1_find查找文本

```
1）find
    目的：查找符合条件的文件
    
1）在哪些目录中查找
2）查找的内容

格式： find    目录名     选线      查找条件

举例：
    1）find /home/book/Desktop/works/hardwork/001_linux_basic/dira/ -name "test1.txt"
    说明：
        a）/home/book/Desktop/works/hardwork/001_linux_basic/dira/ 
        b)-name 表明一名字来查找文件
        c)"test1.txt",就指明查找名为test1.txt的文件
    同理：
        find /home/book/Desktop/works/hardwork/001_linux_basic/dira/ -name "*.txt"
        查找指定目录下阿米娜所有以.txt结尾的文件，其中*是通配符。
        find /home/book/Desktop/works/hardwork/001_linux_basic/-name "dira"
    注意：
        1）如果没有指明查找目录，则为当前目录
            find  -name "test1.txt" 
            find . -name "test1.txt"
            都是一样的功能
        2）find还有一些高级用法，如查找最近几天（几小时）之内（之前）有变化的文件
            find /home -mtime -2    查找/home目录下两天内有变化的文件
```

### linux进阶命令2_grep查找字符串

```
grep
目的：使用grep命令来查找文件中符合条件的字符串
格式：grep 【选项】    【查找模式】  【文件名】

将dira目录的test1.txt和dirb目录的test1.txt都含有如下内容：
    aaa
    AAAAAAAAAA
    abc
    abcabcabc
    cbacbacba
    match_pattern
    nand->erase

查找字符串是希望显示如下内容：
    1）所在的文件名----grep查找时默认已经显示目标文件名
    2）所在的行号------使用-n选项
    
grep -rn "字符串" 文件名
    r（recursive）：递归查找
    n（number）：显示目标位置的行号
    字符串：要查找的字符串
    文件名：要查找的目标文件，如果是*则表示查找当前目标下所有文件和目录
    
举例：
    grep -n "abc" test1.txt     在test1.txt中查找字符串abc
    grep -rn "abc" *             在当前目标文件递归查找字符串abc
    
注意：
1）可以加入-w全字匹配
```

### linux进阶命令3_file查找字符串

```
file
目的：识别文件类型
格式：file 文件名

linux下一切皆文件

举例：
    file ~/.bashrc      为ASCII 编码的text类型
    file ~/.vimrc       为UTF-8 Unicode（编码，统一码） text类型（文本文件）
    file ~/Pictures/*  如图形文件JPEG/PNG/BMP
    file ~/Desktop    为directory表明这是一个目录
    file /bin/pwd      出现ELF 64-bit LSB executable，即为ELF格式的可执行文件
    file /dev/*         出现character special（字符设备文件）、block special（块设备文件）等
```

### linux进阶命令4_which和whereis查找命令所在位置

```
which和whereis
目的：查找命令或应用程序色所在位置
格式：which    命令名/应用程序名

在终端上执行pwd实际上是去执行了/bin/pwd
举例：
    which pwd   定位到/bin/pwd
    which gcc   定位到/usr/bin/gcc
    whereis pwd   
           查找到可执行程序的位置/bin/pwd和手册页/usr/share/man/man1/pwd.1.gz
```

### linux进阶命令5_gzip和bzip2个单文件的压缩和解压

```
压缩
1.压缩概念
1）压缩的目的：
在网络传递文件时，可以先将文件压缩，然后传递压缩后的文件，从而减少网络带宽。
接受者接受文件后，解压即可。

2）压缩的类型
   有损压缩、无损压缩。
   a)有损压缩：
    如mp4视频文件，即使压缩过程中，减少了很多帧的数据，
    对观看者而言，也没有影响。当然mp3音乐文件也是有损压缩。
   b)无损压缩：
    如普通文件的压缩，为了保证信息的正确传递，
    不希望文件经过压缩或解压后，出现问题。

2.linux下常用的压缩命令
   小节：
    单个文件的压缩（解压）使用gzip和bzip2
    多个文件和目录使用tar
    
   gzip的常用选项
    -l(list)	列出压缩文件的内容
    -k(keep)	在压缩或解压时，保留输入文件。
    -d(decompress)	将压缩文件进行解压缩

1)查看
    gzip -l 压缩文件名
    比如：gzip -l pwd.1.gz
    
2）解压
    gzip -kd 压缩文件名
    gzip -kd pwd.1.gz
    该压缩文件是以.gz结尾的单个文件
    
3）压缩
    gzip -k 原文件名
    gzip -k mypwd.1
    得到一个.gz结尾的压缩文件
    
注意：
    1）如果gzip不加任何选项，此时为压缩，压缩完改文件会生成后缀为.gz
    的压缩文件，并删除原有的文件，所以所，推荐使用gzip -k，来压缩文件。
    2）相同文件内容，如果文件名不同，压缩后的大小也不同。
    3)gzip只能压缩单个文件，不能压缩目录
    
bzip2来压缩单个文件
bzip2的常用选项
-k(keep)	在压缩或解压时，保留输入文件。
-d(decompress)	将压缩文件进行解压缩

1)压缩
    bzip2 -k 源文件名
    bzip2 -k mypwd.1
    得到一个以.bz2结尾的压缩文件
    
2）解压
    bzip2 -kd 压缩文件名
    bzip2 -kd mypwd.1.bz2
    
注意：
1）如果bzip2不加任何选项，此时为压缩，压缩完改文件会生成后缀为.bz2
    的压缩文件，并删除原有的文件，所以所，推荐使用bzip2 -k，来压缩文件。
2)gzip只能压缩单个文件，不能压缩目录。

单个文件的压缩使用gzip或bzip2，
压缩文件有两个参数：1）压缩时间    2）压缩比

一般情况下，小文件使用gzip来压缩，大文件使用bzip2来压缩
mypwd.1源文件大小1477字节，
    gzip压缩后mypwd.1.gz是879字节
    bzip2压缩后mypwd.1.bz2是936字节
    
ls.1源文件大小7664字节
    gzip压缩后ls.1.gz是3138字节
    bzip2压缩后ls.1.bz2是3064字节
```

### linux进阶命令6_tar多个文件和目录的压缩和解压

```
gzip、bizp2只能对一个文件进行压缩，而不能对多个文件和目录进行压缩。
所以需要tar来对多个目录、文件进行打包和压缩。

tar常用选项
-c(create) 表示创建用来生成文件包
-x：表示提取，从文件包中提取文件
-t可以查看压缩的文件。
-z使用gzip方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。
-j使用bzip2方式进行处理，它与”c“结合就表示压缩，与”x“结合就表示解压缩。
-v(verbose)详细报告tar处理的信息
-f(file)表示文件，后面接着一个文件名。 
-C  <指定目录>    解压到指定目录

1.tar打包、gzip压缩
1）压缩
    tar -czvf 压缩文件名 目录名
    如：tar czvf dira.tar.gz dira
    
    注意：
    tar -czvf与tar czvf是一样的效果，所以说，后面同意取消“-”。

2)查看
    tar tvf 压缩文件名
    如：tar tvf dira.tar.gz
    
3）解压
    tar xzvf 压缩文件名
    tar xzvf 压缩文件名  -C  指定目录
    tar xzvf dira.tar.gz    解压到当前目录
    如：tar xzvf dira.tar.gz -C /home/book    解压到/home/book/目录

1.tar打包、bzip2压缩
1）压缩
    tar -cjvf 压缩文件名 目录名
    如：tar cjvf dira.tar.bz2 dira

2)查看
    tar tvf 压缩文件名
    如：tar tvf dira.tar.bz2
    
3）解压
    tar xjvf 压缩文件名
    如：tar xjvf dira.tar.bz2     解压到当前目录
    tar xjvf 压缩文件名  -C  指定目录
    如：tar xjvf dira.tar.bz2 -C /home/book/      解压到/home/book/目录
```