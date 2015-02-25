#### 关机流程
android系统都有一个关机按键，长按这个按键系统会进行关机操作。具体实现流程如下：
在android层通过系统调用reboot（arg）调用内核中的sys_reboot,具体流程如下：
```  
reboot——>sys_reboot()——>kernel_power_off()——>machine_power_off()——>pm_power_off();
```
pm_power_off是一个函数指针，他指向和具体硬件平台相关的关机操作函数。和sys_reboot相关的系统调用在source/kernel/sys.c中实现，然后在source/asm-arm/unistd.h中添加系统调用号。然后在source/arch/arm/kernel/entry-common.S中对sys_call_table(系统调用表)进行定义，具体的表在source/arch/arm/kernel/call.S中实现。
#### Reboot流程
Linux下的关机和重启流程对于一般的桌面应用和网络服务器来说并不重要，但是在用户自己定义的嵌入式系统内核中就有一定的研究意义，通过了解Linux 关机重启的流程，我们对它可以修改和自定义，甚至以此为基础开发出全新的功能来。
#### 1.概述
在linux下的关机和重启可能由两种行为引发，一是通过用户编程，一是系统自己产生的消息。用户和系统进行交互的方式也有两个，一个是系统调用：sys_reboot，另一个就是apm或则acpi的设备文件，通过对其操作也可以使系统关机或者重启。
#### 2.通过系统调用sys_reboot的重启
这个系统调用定义了一系列的MAGIC_NUMBER，在调用的开始部分首先检查MAGIC_NUMBER是否正确，只有正确才继续向下运行。在重启的时候转向分支case LINUX_REBOOT_CMD_RESTART:
首先使用notifier_call_chain向其它部分发出重启的消息，然后调用machine_restart函数完成重启。
machine_restart函数的开始部分有一段SMP相关的代码，主要完成多CPU时由一个CPU完成重启操作，其它CPU处于等待状态。之后系统根据一个变量reboot_thru_bios的内容判断重启方式，通过阅读reboot_setup我们可以得知，这个参数的内容是在系统启动时指定的，决定了是否利用bios，事实上是系统复位后的入口(FFFF：0000)地址的程序进行重启。在不通过bios进行重启的情况下，系统首先设定了重启标志，然后向端口0xfe写入数字0x64,这种重启的具体原理我还不大清楚，似乎是模拟了一次reset键的按下，希望大家和我讨论。在通过 bios重启的情况下，系统同样先设定了重启模式，然后切换到了实模式，通过一条ljmp $0xffff,$0x0完成了重启。
#### 3.通过系统调用sys_reboot进行关机
在系统调用的处理分支上，我们可以看到，首先同样是检查MAGIC_NUMBER，然后在case LINUX_REBOOT_CMD_POWER_OFF的执行流程里面，又是使用notifier_call_chain发出了关闭计算机电源的消息，紧接着执行了machine_power_off 函数。我们machine_power_off函数中可以看到，如果pm_power_off这个函数指针不为空，那么系统就会通过调用这个函数进行关机。在apm已经加载的情况下(SMP除外)，实际上pm_power_off函数实际上指向了apm.c中的apm_power_off，在这个函数里系统通过apm_info结构里的值，使用切换到实模式关机，或者使用apm_bios_call_simple函数调用保护模式下的apm接口关机两种方法。