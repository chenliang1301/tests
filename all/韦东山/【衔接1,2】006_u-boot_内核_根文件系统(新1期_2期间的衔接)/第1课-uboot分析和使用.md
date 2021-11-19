# 第1课-uboot分析和使用

- [ ]  PC机和嵌入式系统如何工作？

![%E7%AC%AC1%E8%AF%BE-uboot%E5%88%86%E6%9E%90%E5%92%8C%E4%BD%BF%E7%94%A8%20daa9deaecf9b4a2b8404e958bf0c4bfa/Untitled.png](D:\08 git\notion\all\韦东山\【衔接1,2】006_u-boot_内核_根文件系统(新1期_2期间的衔接) 679b6dacc6574fe7b7818f57a9c94023\images\Untitled-1636554493200.png)

移至uboot步骤
1、解压缩 tar xjf u-boot-1.1.6.tar.bz2
2、打补丁 patch -p1（忽略文件目录之前的东西） < ../u-boot-1.1.6_jz2440.patch
3、配置 make 100ask24x0_config
4、编译 make

```makefile
MKCONFIG	:= $(SRCTREE)/mkconfig

@$(MKCONFIG) $(@:_config=) arm arm920t 100ask24x0 NULL s3c24x0

mkconfig 100ask24x0 arm arm920t 100ask24x0 NULL s3c24x0

OBJS = cpu/arm920/start.o

LIBS = lib_generic/libgeneric.a
LIBS += board/100ask24x0/lib100ask24x0.a
LIBS += cpu/arm920/libarm920.a

$(obj)u-boot:		depend version $(SUBDIRS) $(OBJS) $(LIBS) $(LDSCRIPT)
		UNDEF_SYM=`$(OBJDUMP) -x $(LIBS) |sed  -n -e 's/.*\(__u_boot_cmd_.*\)/-u\1/p'|sort|uniq`;\
		cd $(LNDIR) && $(LD) $(LDFLAGS) $$UNDEF_SYM $(__OBJS) \
			--start-group $(__LIBS) --end-group $(PLATFORM_LIBS) \
			-Map u-boot.map -o u-boot

config.mk:189:LDFLAGS += -Bstatic -T $(LDSCRIPT) -Ttext $(TEXT_BASE) $(PLATFORM_LDFLAGS)

TEXT_BASE:
board/100ask24x0/config.mk 

UNDEF_SYM=`arm-linux-objdump -x lib_generic/libgeneric.a board/100ask24x0/lib100ask24x0.a cpu/arm920t/libarm920t.a cpu/arm920t/s3c24x0/libs3c24x0.a lib_arm/libarm.a fs/cramfs/libcramfs.a fs/fat/libfat.a fs/fdos/libfdos.a fs/jffs2/libjffs2.a fs/reiserfs/libreiserfs.a fs/ext2/libext2fs.a net/libnet.a disk/libdisk.a rtc/librtc.a dtt/libdtt.a drivers/libdrivers.a drivers/nand/libnand.a drivers/nand_legacy/libnand_legacy.a drivers/usb/libusb.a drivers/sk98lin/libsk98lin.a common/libcommon.a |sed  -n -e 's/.*\(__u_boot_cmd_.*\)/-u\1/p'|sort|uniq`;\
        cd /home/book/Desktop/works/system/u-boot-1.1.6 && 
arm-linux-ld -Bstatic -T /home/book/Desktop/works/system/u-boot-1.1.6/board/100ask24x0/u-boot.lds -Ttext 0x33F80000  $UNDEF_SYM cpu/arm920t/start.o \
                --start-group lib_generic/libgeneric.a board/100ask24x0/lib100ask24x0.a cpu/arm920t/libarm920t.a cpu/arm920t/s3c24x0/libs3c24x0.a lib_arm/libarm.a fs/cramfs/libcramfs.a fs/fat/libfat.a fs/fdos/libfdos.a fs/jffs2/libjffs2.a fs/reiserfs/libreiserfs.a fs/ext2/libext2fs.a net/libnet.a disk/libdisk.a rtc/librtc.a dtt/libdtt.a drivers/libdrivers.a drivers/nand/libnand.a drivers/nand_legacy/libnand_legacy.a drivers/usb/libusb.a drivers/sk98lin/libsk98lin.a common/libcommon.a --end-group  \
												-Map u-boot.map -o u-boot
```

![%E7%AC%AC1%E8%AF%BE-uboot%E5%88%86%E6%9E%90%E5%92%8C%E4%BD%BF%E7%94%A8%20daa9deaecf9b4a2b8404e958bf0c4bfa/u-boot.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162248702.png)

![%E7%AC%AC1%E8%AF%BE-uboot%E5%88%86%E6%9E%90%E5%92%8C%E4%BD%BF%E7%94%A8%20daa9deaecf9b4a2b8404e958bf0c4bfa.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162248703.png)

uboot
1.最终目的启动内核
2.功能
    1）硬件相关
         关看门狗、初始化时钟、初始化SDRAM
    2）非硬件
       读从Flash出内核

![%E7%AC%AC1%E8%AF%BE-uboot%E5%88%86%E6%9E%90%E5%92%8C%E4%BD%BF%E7%94%A8%20daa9deaecf9b4a2b8404e958bf0c4bfa%201.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162248704.png)

md.w 0

argv[0] md.w

argv[1] 0

#define Struct_Section **attribute** ((unused,section (".u_boot_cmd")))

bootcmd=nand read.jffs2 0x30007FC0 

kernel;

bootm 0x30007FC0

```c
U_BOOT_CMD(
 	bootm,	CFG_MAXARGS,	1,	do_bootm,
 	"bootm   - boot application image from memory\n",
 	"[addr [arg ...]]\n    - boot application image stored in memory\n"
 	"\tpassing arguments 'arg ...'; when booting a Linux kernel,\n"
 	"\t'arg' can be the address of an initrd image\n"
#ifdef CONFIG_OF_FLAT_TREE
	"\tWhen booting a Linux kernel which requires a flat device-tree\n"
	"\ta third argument is required which is the address of the of the\n"
	"\tdevice-tree blob. To boot that kernel without an initrd image,\n"
	"\tuse a '-' for the second argument. If you do not pass a third\n"
	"\ta bd_info struct will be passed instead\n"
#endif
);

#define U_BOOT_CMD(name,maxargs,rep,cmd,usage,help) \
cmd_tbl_t __u_boot_cmd_##name Struct_Section = {#name, maxargs, rep, cmd, usage, help}

cmd_tbl_t __u_boot_cmd_bootm **attribute** ((unused,section (".u_boot_cmd"))) = {#name, maxargs, rep, cmd, usage, help}

```

![%E7%AC%AC1%E8%AF%BE-uboot%E5%88%86%E6%9E%90%E5%92%8C%E4%BD%BF%E7%94%A8%20daa9deaecf9b4a2b8404e958bf0c4bfa/u-boot%201.png](https://cdn.jsdelivr.net/gh/chenliang1301/Images@main/NotesImages/202111162248705.png)