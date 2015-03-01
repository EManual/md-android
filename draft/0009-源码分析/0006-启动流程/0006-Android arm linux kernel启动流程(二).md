下面我们来看看kernel产生的过程，老方法，从Makefile开始/arch/arm/boot/Makefile ～
```  
$(obj)/Image: vmlinux FORCE 
$(call if_changed,objcopy) 
@echo ' Kernel: $@ is ready' 
$(obj)/compressed/vmlinux: $(obj)/Image FORCE 
$(Q)$(MAKE) $(build)=$(obj)/compressed $@ 
$(obj)/zImage: $(obj)/compressed/vmlinux FORCE 
$(call if_changed,objcopy) 
@echo ' Kernel: $@ is ready' 
```
我们分解地来看各个步骤，第一个是将vmlinux经过objcopy后生成一个未经压缩的raw binary(Image 4M左右)，这里的vmlinux是我们编译链接以后生成的vmlinx，大概60多M。这里稍微说一下这个objcopy，在启动的时候ELF格式是没法执行的，ELF格式的解析是在kernel启动以后有了操作系统之后才能进行的。因为虽然我们编出的img虽然被编成ELF格式，但要想启动起来必须将其转化成原始的二进制格式，我们可以多照着man objcopy和OBJCOPYFLAGS    :=-O binary -R .note -R .note.gnu.build-id -R .comment -S(arch/arm/Makefile)来看看这些objcopy具体做了什么事情 
得到Image以后，再将这个Image跟解压代码合成一个vmlinux，具体的我们可以看看arch/arm/boot/compressed/Makefile：
```  
$(obj)/vmlinux: $(obj)/vmlinux.lds $(obj)/$(HEAD) $(obj)/piggy.o / 
$(addprefix $(obj)/, $(OBJS)) FORCE 
$(call if_changed,ld) 
@: 
$(obj)/piggy.gz: $(obj)/../Image FORCE 
$(call if_changed,gzip) 
$(obj)/piggy.o: $(obj)/piggy.gz FORCE 
```
从这里我们就可以看出来实际上这个vmlinux就是将Image压缩以后根据vmlinux.lds与解压代码head.o和misc.o链接以后生成的一个elf，而且用readelf或者objdump可以很明显地看到解压代码是PIC的，所有的虚拟地址都是相对的，没有绝对地址。这里的vmlinx.lds可以对照着后面的head.s稍微看一下～得到压缩以后的vmlinx以后再将这个vmlinx经过objcopy以后就得到我们的zImage了，然后拷贝到out目录下就是我们的kernel了
在这里要强调几个地址，这些地址定义在arch/arm/mach-msm/makefile.boot里面，被arch/arm/boot/Makefile调用，其中zreladdr-y就是我们的kernel被解压以后要释放的地址了，解压代码跑完以后就会跳到这个地址来执行kernel的启动。不过这里还有其他两个PHYS，跟前面定义在boardconfig.h里面的值重复了，不知道这两个值在这里定义跟前面的值是一种什么关系？
好啦，讲到这里我们基本就知道boot.img的构成了，下面我们就从解压的代码开始看看arm linux kernel启动的一个过程，这个解压的source就是/arch/arm/boot/compressed/head.S。要看懂这个汇编需要了解GNU ASM以及ARM汇编指令，ARM指令就不说了，ARM RVCT里面的文档有得下，至于GNU ASM，不需要消息了解的话主要是看一下一些伪指令的含义
那么我们现在就开始分析这个解压的过程：
1）bootloader会传递2个参数过来，分别是r1=architecture ID, r2=atags pointer。head.S从哪部分开始执行呢，这个我们可以看看vmlinx.lds：
```  
ENTRY(_start) 
SECTIONS 
{ 
. = 0; 
_text = .; 
.text : { 
_start = .; 
*(.start) 
*(.text) 
*(.text.*) 
*(.fixup) 
*(.gnu.warning) 
*(.rodata) 
*(.rodata.*) 
*(.glue_7) 
*(.glue_7t) 
*(.piggydata) 
. = ALIGN(4); 
} 
```
可以看到我们最开始的section就是.start，所以我们是从start段开始执行的。ELF对程序的入口地址是有定义的，这可以参照*.lds的语法规则里面有描述，分别是GNU LD的-E ---> *.lds里面的ENTRY定义  ---> start Symbol  ---> .text section --->0。在这里是没有这些判断的，因为还没有操作系统，bootloader会直接跳到这个start的地址开始执行。
在这里稍微带一句，如果觉得head.S看的不太舒服的话，比如有些跳转并不知道意思，可以直接objdump vmlinx来看，dump出来的汇编的流程就比较清晰了。
```  
1: mov r7, r1 @ save architecture ID 
mov r8, r2 @ save atags pointer 
ifndef __ARM_ARCH_2__ 
/* 
* Booting from Angel - need to enter SVC mode and disable 
* FIQs/IRQs (numeric definitions from angel arm.h source). 
* We only do this if we were in user mode on entry. 
*/ 
mrs r2, cpsr @ get current mode 
tst r2, 3 @ not user? 
bne not_angel @ 如果不是 
mov r0, 0x17 @ angel_SWIreason_EnterSVC 
swi 0x123456 @ angel_SWI_ARM 
not_angel: 
mrs r2, cpsr @ turn off interrupts to 
orr r2, r2, 0xc0 @ prevent angel from running 
msr cpsr_c, r2 
```
上面首先保存r1和r2的值，然后进入超级用户模式，并关闭中断。
```  
.text 
adr r0, LC0 
ldmia r0, {r1, r2, r3, r4, r5, r6, ip, sp} 
subs r0, r0, r1 @ calculate the delta offset 
@ if delta is zero, we are 
beq not_relocated @ running at the address we 
@ were linked at. 
```