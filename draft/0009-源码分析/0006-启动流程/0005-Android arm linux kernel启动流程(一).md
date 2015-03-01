虽然这里的Arm Linux kernel前面加上了Android，但实际上还是和普遍Arm linux kernel启动的过程一样的，这里只是结合一下Android的Makefile，讲一下bootimage生成的一个过程。这篇文档主要描述bootimage的构造，以及kernel真正执行前的解压过程。
在了解这些之前我们首先需要了解几个名词，这些名词定义在/Documentation/arm/Porting里面，这里首先提到其中的几个，其余几个会在后面kernel的执行过程中讲述：
1）ZTEXTADDR  boot.img运行时候zImage的起始地址，即kernel解压代码的地址。这里没有虚拟地址的概念，因为没有开启MMU，所以这个地址是物理内存的地址。解压代码不一定需要载入RAM才能运行，在FLASH或者其他可寻址的媒体上都可以运行。
2）ZBSSADDR 解压代码的BSS段的地址，这里也是物理地址。
3）ZRELADDR  这个是kernel解压以后存放的内存物理地址，解压代码执行完成以后会跳到这个地址执行kernel的启动，这个地址和后面kernel运行时候的虚拟地址满足：__virt_to_phys(TEXTADDR) = ZRELADDR。
4）INITRD_PHYS  Initial Ram Disk存放在内存中的物理地址，这里就是我们的ramdisk.img。
5）INITRD_VIRT  Initial Ram Disk运行时候虚拟地址。
6）PARAMS_PHYS 内核启动的初始化参数在内存上的物理地址。
下面我们首先来看看boot.img的构造，了解其中的内容对我们了解kernel的启动过程是很有帮助的。首先来看看Makefile是如何产生我们的boot.img的：
```  
out/host/linux-x86/bin/mkbootimg-msm7627_ffa  --kernel out/target/product/msm7627_ffa/kernel --ramdisk out/target/product/msm7627_ffa/ramdisk.img --cmdline "mem=203M console=ttyMSM2,115200n8 androidboot.hardware=qcom" --output out/target/product/msm7627_ffa/boot.img
```
根据上面的命令我们可以首先看看mkbootimg-msm7627ffa这个工具的源文件：system/core/mkbootimg.c。看完之后我们就能很清晰地看到boot.img的内部构造，它是由boot header /kernel  /ramdisk /second stage构成的，其中前3项是必须的，最后一项是可选的。
```  
 /* 
 * * +-----------------+ 
 * * | boot header | 1 page 
 * * +-----------------+ 
 * * | kernel | n pages 
 * * +-----------------+ 
 * * | ramdisk | m pages 
 * * +-----------------+ 
 * * | second stage | o pages 
 * * +-----------------+ 
 * * 
 * * n = (kernel_size + page_size - 1) / page_size 
 * * m = (ramdisk_size + page_size - 1) / page_size 
 * * o = (second_size + page_size - 1) / page_size 
 * * 
 * * 0. all entities are page_size aligned in flash 
 * * 1. kernel and ramdisk are required (size != 0) 
 * * 2. second is optional (second_size == 0 -> no second) 
 * * 3. load each element (kernel, ramdisk, second) at 
 * * the specified physical address (kernel_addr, etc) 
 * * 4. prepare tags at tag_addr. kernel_args[] is 
 * * appended to the kernel commandline in the tags. 
 * * 5. r0 = 0, r1 = MACHINE_TYPE, r2 = tags_addr 
 * * 6. if second_size != 0: jump to second_addr 
 * * else: jump to kernel_addr 
 */ 
```
关于boot header这个数据结构我们需要重点注意，在这里我们关注其中几个比较重要的值，这些值定义在boot/boardconfig.h里面，不同的芯片对应vendor下不同的boardconfig，在这里我们的值分别是（分别是kernel/ramdis/tags载入ram的物理地址）：
```  
define PHYSICAL_DRAM_BASE 0x00200000 
define KERNEL_ADDR (PHYSICAL_DRAM_BASE + 0x00008000) 
define RAMDISK_ADDR (PHYSICAL_DRAM_BASE + 0x01000000) 
define TAGS_ADDR (PHYSICAL_DRAM_BASE + 0x00000100) 
define NEWTAGS_ADDR (PHYSICAL_DRAM_BASE + 0x00004000) 
```
上面这些值分别和我们开篇时候提到的那几个名词相对应，比如kernel_addr就是ZTEXTADDR，RAMDISK_ADDR就是INITRD_PHYS,而TAGS_ADDR就是PARAMS_PHYS。bootloader会从boot.img的分区中将kernel和ramdisk分别读入RAM上面定义的地址中，然后就会跳到ZTEXTADDR开始执行。
基本了解boot.img的内容之后我们来分别看看里面的ramdisk.img和kernel又是如何产生的，以及其包含的内容。从简单的说起，我们先看看ramdisk.img，这里首先要强调一下这个ramdisk.img在arm linux中的作用。它在kernel启动过程中充当着第一阶段的文件系统，是一个CPIO格式打成的包。通俗上来讲他就是我们将生成的root目录，用CPIO方式进行了打包，然后在kernel启动过程中会被mount作为文件系统，当kernel启动完成以后会执行init，然后将system.img再mount进来作为Android的文件系统。在这里稍微解释下这个mount的概念，所谓mount实际上就是告诉linux虚拟文件系统它的根目录在哪，就是说我这个虚拟文件系统需要操作的那块区域在哪，比如说ramdisk实际上是我们在内存中的一块区域，把它作为文件系统的意思实际上就是告诉虚拟文件系统你的根目录就在我这里，我的起始地址赋给你，你以后就能对我进行操作了。实际上我们也可以使用rom上的一块区域作为根文件系统，但是rom相对ram慢，所以这里使用ramdisk。然后我们在把system.img mount到ramdisk的system目录，实际上就是将system.img的地址给了虚拟文件系统，然后虚拟文件系统访问system目录的时候会重新定位到对system.img的访问。我们可以看看makefile是如何生成它的：
```  
out/host/linux-x86/bin/mkbootfs  
out/target/product/msm7627_ffa/root | out/host/linux-x86/bin/minigzip > out/target/product/msm7627_ffa/ramdisk.img  
```