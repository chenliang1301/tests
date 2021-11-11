# 移植最新的u-boot

1、下载、建立source insight工程、编译、烧写、如果无运行分析原因
tar xjf u-boot-2012.04.01.tar.bz2
cd u-boot-2012.04.01
make smdk2410_config
make

2. 分析u-boot: 通过链接命令分析组成文件、阅读代码分析启动过程

a. 初始化硬件：关看门狗、设置时钟、设置SDRAM、初始化NAND FLASH
b. 如果bootloader比较大，要把它重定位到SDRAM
c. 把内核从NAND FLASH读到SDRAM
d. 设置"要传给内核的参数"
e. 跳转执行内核

2.1 set the cpu to SVC32 mode
2.2 turn off the watchdog
2.3 mask all IRQs by setting all bits in the INTMR
2.4 设置时钟比例
2.5 设置内存控制器
2.6 设置栈，调用C函数board_init_f
2.7 调用函数数组init_sequence里的各个函数
2.7.1 board_early_init_f : 设置系统时钟、设置GPIO
......
2.8 重定位代码:
2.8.1 从NOR FLASH把代码复制到SDRAM
2.8.2 程序的链接地址是0，访问全局变量、静态变量、调用函数时是使"基于0地址编译得到的地址"
      现在把程序复制到了SDRAM
      需要修改代码，把"基于0地址编译得到的地址"改为新地址
2.8.3 程序里有些地址在链接时不能确定，要到运行前才能确定：fixabs
2.9 clear_bss
2.10 调用C函数board_init_r：第2阶段的代码

可以修改配置定义CONFIG_S3C2440

3. 修改U-BOOT代码
3.1 建一个单板
cd board/samsung/
cp smdk2410 smdk2440 -rf
cd ../../include/configs/
cp smdk2410.h smdk2440.h
修改boards.cfg:
仿照
smdk2410                     arm         arm920t     -                   samsung        s3c24x0
添加：
smdk2440                     arm         arm920t     -                   samsung        s3c24x0

3.2 烧写看结果
3.3 调试： 
a. 阅读代码发现不足：UBOOT里先以60MHZ的时钟计算参数来设置内存控制器，但是MPLL还未设置
   处理措施: 把MPLL的设置放到start.S里，取消board_early_init_f里对MPLL的设置


![%E7%A7%BB%E6%A4%8D%E6%9C%80%E6%96%B0%E7%9A%84u-boot%20e800565728074ef19e7042726f630f9b/Untitled.png](D:\08 git\notion\all\韦东山\images\Untitled-1636554428359.png)

![%E7%A7%BB%E6%A4%8D%E6%9C%80%E6%96%B0%E7%9A%84u-boot%20e800565728074ef19e7042726f630f9b/Untitled%201.png](D:\08 git\notion\all\韦东山\images\Untitled 1-1636554430971.png)

   编译出来的uboot非常大，可以先烧写主光盘里的u-boot.bin到nor，然后用这个uboot来烧写新的uboot

3.4 乱码，查看串口波特率的设置，发现在get_HCLK里没有定义CONFIG_S3C2440
    处理措施：include/configs/smdk2440.h: 去掉CONFIG_S3C2410
                                          #define CONFIG_S3C2440
                                          //#define CONFIG_CMD_NAND

![%E7%A7%BB%E6%A4%8D%E6%9C%80%E6%96%B0%E7%9A%84u-boot%20e800565728074ef19e7042726f630f9b/Untitled%202.png](D:\08 git\notion\all\韦东山\images\Untitled 2-1636554433311.png)

3.5 修改UBOOT支持NAND启动
    原来的代码在链接时加了"-pie"选项, 使得u-boot.bin里多了"*(.rel*)", "*(.dynsym)"
    使得程序非常大，不利于从NAND启动(重定位之前的启动代码应该少于4K)
3.5.1 去掉 "-pie"选项
      arch/arm/config.mk:75:LDFLAGS_u-boot += -pie 去掉这行
      
3.5.2 参考"毕业班第1课"的start.S, init.c来修改代码
      把init.c放入board/samsung/smdk2440目录, 修改Makefile
      修改CONFIG_SYS_TEXT_BASE为0x33f80000
      修改start.S

3.5.3 修改board_init_f, 把relocate_code去掉

![%E7%A7%BB%E6%A4%8D%E6%9C%80%E6%96%B0%E7%9A%84u-boot%20e800565728074ef19e7042726f630f9b/Untitled%203.png](D:\08 git\notion\all\韦东山\images\Untitled 3.png)

3.5.4 修改链接脚本: 把start.S, init.c, lowlevel.S等文件放在最前面
      ./arch/arm/cpu/u-boot.lds：
      
      board/samsung/smdk2440/libsmdk2440.o

3.6 修改UBOOT支持NOR FLASH
  drivers\mtd\jedec_flash.c 加上新的型号
  #define CONFIG_SYS_MAX_FLASH_SECT	(128)

  修复了重定时留下来的BUG：SP要重新设置

3.7 修改UBOOT支持NAND FLASH
    修改：include/configs/smdk2440.h: #define CONFIG_CMD_NAND
    
    把drivers\mtd\nand\s3c2410_nand.c复制为s3c2440_nand.c


分析过程：
nand_init
	nand_init_chip
		board_nand_init
			设置nand_chip结构体, 提供底层的操作函数
		nand_scan
			nand_scan_ident
				nand_set_defaults
					chip->select_chip = nand_select_chip;
					chip->cmdfunc = nand_command;
					chip->read_byte = busw ? nand_read_byte16 : nand_read_byte;
					
				nand_get_flash_type
					chip->select_chip
					chip->cmdfunc(mtd, NAND_CMD_RESET, -1, -1);
							nand_command()  // 即可以用来发命令，也可以用来发列地址(页内地址)、行地址(哪一页)
								chip->cmd_ctrl
										s3c2440_hwcontrol
								
					chip->cmdfunc(mtd, NAND_CMD_READID, 0x00, -1);
					*maf_id = chip->read_byte(mtd);
					*dev_id = chip->read_byte(mtd);

3.8 修改UBOOT支持DM9000网卡
	eth_initialize
		board_eth_init
			cs8900_initialize

*** ERROR: `ethaddr' not set
set ipaddr 192.168.1.17
set ethaddr 00:0c:29:4d:e4:f4
set serverip 192.168.1.3

usb 1 30000000

protect off all
erase 0 7ffff
cp.b 30000000 0 80000

![%E7%A7%BB%E6%A4%8D%E6%9C%80%E6%96%B0%E7%9A%84u-boot%20e800565728074ef19e7042726f630f9b/Untitled%204.png](D:\08 git\notion\all\韦东山\images\Untitled 4.png)

4. 易用性修裁剪及制作补丁					

内核打印出来的分区信息
0x00000000-0x00040000 : "bootloader"
0x00040000-0x00060000 : "params"
0x00060000-0x00260000 : "kernel"
0x00260000-0x10000000 : "root"

nand erase 60000 200000
nand write 30000000 60000 200000

tftp 30000000 uImage
nand erase.part kernel
nand write 30000000 kernel

烧写JFFS2
tftp 30000000 fs_mini_mdev.jffs2
nand erase.part rootfs
nand write.jffs2 30000000 0x00260000 5b89a8

set bootargs console=ttySAC0,115200 root=/dev/mtdblock3 rootfstype=jffs2

烧写YAFFS
tftp 30000000 fs_mini_mdev.yaffs2
nand erase.part rootfs
nand write.yaffs 30000000 260000  889bc0

更新UBOOT:
tftp 30000000 u-boot_new.bin; protect off all; erase 0 3ffff; cp.b 30000000 0 40000

制作补丁：
diff -urN u-boot-2012.04.01 u-boot-2012.04.01_100ask > u-boot-2012.04.01_100ask.patch

分析"重定位之修改代码为新地址":
#ifndef CONFIG_SPL_BUILD
	/*
	 * fix .rel.dyn relocations
	 */
	ldr	r0, _TEXT_BASE		/* r0 <- Text base */
	// r0=0, 代码基地址
	
	sub	r9, r6, r0		/* r9 <- relocation offset */
	// r9 = r6-r0 = 0x33f41000 - 0 = 0x33f41000
	
	ldr	r10, _dynsym_start_ofs	/* r10 <- sym table ofs */
	// r10 = 00073608
	
	add	r10, r10, r0		/* r10 <- sym table in FLASH */
	// r10 = 00073608 + 0 = 00073608
	
	ldr	r2, _rel_dyn_start_ofs	/* r2 <- rel dyn start ofs */
	// r2=0006b568
	
	add	r2, r2, r0		/* r2 <- rel dyn start in FLASH */
	// r2=r2+r0=0006b568
	
	ldr	r3, _rel_dyn_end_ofs	/* r3 <- rel dyn end ofs */
	// r3=00073608
	
	add	r3, r3, r0		/* r3 <- rel dyn end in FLASH */
	// r3=r3+r0=00073608

fixloop:
	ldr	r0, [r2]		/* r0 <- location to fix up, IN FLASH! */
	1. r0=[0006b568]=00000020
	
	add	r0, r0, r9		/* r0 <- location to fix up in RAM */
	1. r0=r0+r9=00000020 + 0x33f41000 = 0x33f41020
	
	ldr	r1, [r2, #4]
	1. r1=[0006b568+4]=00000017
	
	and	r7, r1, #0xff
	1. r7=r1&0xff=00000017
	
	cmp	r7, #23			/* relative fixup? */
	1. r7 == 23(0x17)
	
	beq	fixrel
	cmp	r7, #2			/* absolute fixup? */
	
	beq	fixabs
	/* ignore unknown type of fixup */
	b	fixnext
fixabs:
	/* absolute fix: set location to (offset) symbol value */
	mov	r1, r1, LSR #4		/* r1 <- symbol index in .dynsym */
	
	add	r1, r10, r1		/* r1 <- address of symbol in table */
	
	ldr	r1, [r1, #4]		/* r1 <- symbol value */
	
	add	r1, r1, r9		/* r1 <- relocated sym addr */
	
	b	fixnext
fixrel:
	/* relative fix: increase location by offset */
	ldr	r1, [r0]
	1. r1=[00000020]=000001e0
	
	add	r1, r1, r9
	1. r1=r1+r9=000001e0 + 0x33f41000 = 33F411E0

fixnext:
	str	r1, [r0]
	1. [0x33f41020] = 33F411E0
	
	add	r2, r2, #8		/* each rel.dyn entry is 8 bytes */
	1. r2=r2+8=0006b568+8=6B570
	
	cmp	r2, r3
	1. 
	
	blo	fixloop
#endif