前面说过解压以后，代码会跳到解压完成以后的vmlinux开始执行，具体从什么地方开始执行我们可以看看生成的vmlinux.lds(arch/arm/kernel/)这个文件：
```  
OUTPUT_ARCH(arm) 
ENTRY(stext) 
jiffies = jiffies_64; 
SECTIONS 
{ 
. = 0x80000000 + 0x00008000; 
.text.head : { 
_stext = .; 
_sinittext = .; 
*(.text.h 
```
很明显我们的vmlinx最开头的section是.text.head，这里我们不能看ENTRY的内容，以为这时候我们没有操作系统，根本不知道如何来解析这里的入口地址，我们只能来分析他的section(不过一般来说这里的ENTRY和我们从seciton分析的结果是一样的)，这里的.text.head section我们很容易就能在arch/arm/kernel/head.S里面找到，而且它里面的第一个符号就是我们的stext：
```  
.section ".text.head", "ax" 
Y(stext) 
msr cpsr_c, PSR_F_BIT | PSR_I_BIT | SVC_MODE @ ensure svc mode 
@ and irqs disabled 
mrc p15, 0, r9, c0, c0 @ get processor id 
bl __lookup_processor_type @ r5=procinfo r9=cpuid 
```
这里的ENTRY这个宏实际我们可以在include/linux/linkage.h里面找到，可以看到他实际上就是声明一个GLOBAL Symbol，后面的ENDPROC和END唯一的区别是前面的声明了一个函数，可以在c里面被调用。
```  
ifndef ENTRY 
define ENTRY(name) / 
.globl name; / 
ALIGN; / 
name: 
endif 
ifndef WEAK 
define WEAK(name) / 
.weak name; / 
name: 
endif 
ifndef END 
define END(name) / 
.size name, .-name 
endif 
[Tags]/* If symbol 'name' is treated as a subroutine (gets called, and returns) 
[Tags]* then please use ENDPROC to mark 'name' as STT_FUNC for the benefit of 
[Tags]* static analysis tools such as stack depth analyzer. 
[Tags]*/ 
ifndef ENDPROC 
define ENDPROC(name) / 
.type name, @function; / 
END(name) 
endif 
```
找到了vmlinux的起始代码我们就来进行分析了，先总体概括一下这部分代码所完成的功能，head.S会首先检查proc和arch以及atag的有效性，然后会建立初始化页表，并进行CPU必要的处理以后打开MMU，并跳转到start_kernel这个symbol开始执行后面的C代码。这里有很多变量都是我们进行kernel移植时需要特别注意的，下面会一一讲到。
在这里我们首先看看这段汇编开始跑的时候的寄存器信息，这里的寄存器内容实际上是同bootloader跳转到解压代码是一样的，就是r1=arch  r2=atag addr。下面我们就具体来看看这个head.S跑的过程：
```  
msr cpsr_c, PSR_F_BIT | PSR_I_BIT | SVC_MODE @ ensure svc mode 
@ and irqs disabled 
mrc p15, 0, r9, c0, c0 @ get processor id 
```
首先进入SVC模式并关闭所有中断，并从arm协处理器里面读到CPU ID，这里的CPU主要是指arm架构相关的CPU型号，比如ARM9，ARM11等等。
然后跳转到__lookup_processor_type，这个函数定义在head-common.S里面，这里的bl指令会保存当前的pc在lr里面，最后__lookup_processor_type会从这个函数返回，我们具体看看这个函数：
```  
__lookup_processor_type: 
adr r3, 3f
ldmda r3, {r5 - r7} 
sub r3, r3, r7 @ get offset between virt&phys 
add r5, r5, r3 @ convert virt addresses to 
add r6, r6, r3 @ physical address space 
1: ldmia r5, {r3, r4} @ value, mask 
and r4, r4, r9 @ mask wanted bits 
teq r3, r4 
beq 2f
add r5, r5, PROC_INFO_SZ @ sizeof(proc_info_list) 
cmp r5, r6 
blo 1b 
mov r5, 0 @ unknown processor 
2: mov pc, lr 
ENDPROC(__lookup_processor_type) 
```