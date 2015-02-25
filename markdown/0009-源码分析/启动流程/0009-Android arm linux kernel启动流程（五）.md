他这里的执行过程其实比较简单就是在__proc_info_begin和__proc_info_end这个段里面里面去读取我们注册在里面的proc_info_list这个结构体，这个结构体的定义在arch/arm/include/asm/procinfo.h，具体实现根据你使用的cpu的架构在arch/arm/mm/里面找到具体的实现，这里我们使用的ARM11是proc-v6.S，我们可以看看这个结构体：
```  
.section ".proc.info.init", alloc, execinstr 
[Tags]/* 
[Tags]* Match any ARMv6 processor core. 
[Tags]*/
.type __v6_proc_info, object 
_proc_info: 
.long 0x0007b000 
.long 0x0007f000 
.long PMD_TYPE_SECT |  
PMD_SECT_BUFFERABLE |  
PMD_SECT_CACHEABLE |  
PMD_SECT_AP_WRITE | 
PMD_SECT_AP_READ 
.long PMD_TYPE_SECT | 
PMD_SECT_XN |  
PMD_SECT_AP_WRITE | 
PMD_SECT_AP_READ 
b __v6_setup 
.long cpu_arch_name 
.long cpu_elf_name 
.long HWCAP_SWP|HWCAP_HALF|HWCAP_THUMB|HWCAP_FAST_MULT|HWCAP_EDSP|HWCAP_JAVA 
.long cpu_v6_name 
.long v6_processor_functions 
.long v6wbi_tlb_fns 
.long v6_user_fns 
.long v6_cache_fns 
.size __v6_proc_info, . - __v6_proc_info 
```
对着.h我们就知道各个成员变量的含义了，他这里lookup的过程实际上是先求出这个proc_info_list的实际物理地址，并将其内容读出，然后将其中的mask也就是我们这里的0x007f000与寄存器与之后与0x007b00进行比较，如果一样的话呢就校验成功了，如果不一样呢就会读下一个proc_info的信息，因为proc一般都是只有一个的，所以这里一般不会循环，如果检测正确寄存器就会将正确的proc_info_list的物理地址赋给寄存器，如果检测不到就会将寄存器值赋0，然后通过LR返回。
```  
bl __lookup_machine_type @ r5=machinfo 
movs r8, r5 @ invalid machine (r5=0)? 
beq __error_a @ yes, error 'a' 
```
检测完proc_info_list以后就开始检测machine_type了，这个函数的实现也在head－common.S里面，我们看看它具体的实现：
```  
__lookup_machine_type: 
adr r3, 3b 
ldmia r3, {r4, r5, r6} 
sub r3, r3, r4 @ get offset between virt&phys 
add r5, r5, r3 @ convert virt addresses to 
add r6, r6, r3 @ physical address space 
1: ldr r3, [r5, MACHINFO_TYPE] @ get machine type 
teq r3, r1 @ matches loader number? 
beq 2f @ found 
add r5, r5, SIZEOF_MACHINE_DESC @ next machine_desc 
cmp r5, r6 
blo 1b 
mov r5, 0 @ unknown machine 
2: mov pc, lr 
ENDPROC(__lookup_machine_type) 
```
这里的过程基本上是同proc的检查是一样的，这里主要检查芯片的类型，比如我们现在的芯片是MSM7X27FFA，这也是一个结构体，它的头文件在arch/arm/include/asm/arch/arch.h里面(machine_desc)，它具体的实现根据你对芯片类型的选择而不同，这里我们使用的是高通的7x27,具体实现在arch/arm/mach-msm/board-msm7x27.c里面，这些结构体最后都会注册到_arch_info_begin和_arch_info_end段里面，具体的大家可以看看vmlinux.lds或者system.map，这里的lookup会根据bootloader传过来的nr来在__arch_info里面的相匹配的类型，没有的话就寻找下一个machin_desk结构体，直到找到相应的结构体，并会将结构体的地址赋值给寄存器，如果没有的话就会赋值为0的。一般来说这里的machine_type会有好几个，因为不同的芯片类型可能使用的都是同一个cpu架构。
对processor和machine的检查完以后就会检查atags parameter的有效性，关于这个atag具体的定义我们可以在./include/asm/setup.h里面看到，它实际是一个结构体和一个联合体构成的结合体，里面的size都是以字来计算的。这里的atags param是bootloader创建的，里面包含了ramdisk以及其他memory分配的一些信息，存储在boot.img头部结构体定义的地址中，具体的大家可以看咱以后对bootloader的分析～
```  
__vet_atags: 
tst r2, 0x3 @ aligned? 
bne 1f 
ldr r5, [r2, 0] @ is first tag ATAG_CORE? 
cmp r5, ATAG_CORE_SIZE 
cmpne r5, ATAG_CORE_SIZE_EMPTY 
bne 1f 
ldr r5, [r2, 4] 
ldr r6, =ATAG_CORE 
cmp r5, r6 
bne 1f 
mov pc, lr @ atag pointer is ok 
1: mov r2, 0 
mov pc, lr 
ENDPROC(__vet_atags) 
```
这里对atag的检查主要检查其是不是以ATAG_CORE开头，size对不对，基本没什么好分析的，代码也比较好看～ 下面我们来看后面一个重头戏，就是创建初始化页表，说实话这段内容我没弄清楚，它需要对ARM VIRT MMU具有相当的理解，这里我没有太多的时间去分析spec，只是粗略了翻了ARM V7的manu，知道这里建立的页表是arm的secition页表，完成内存开始1m内存的映射，这个页表建立在kernel和atag paramert之间，一般是4000-8000之间～具体的代码和过程我这里就不贴了，大家可以看看参考的链接，看看其他大虾的分析，我还没怎么看明白，等以后仔细研究ARM MMU的时候再回头来仔细研究了，不过代码虽然不分析，这里有几个重要的地址需要特别分析下～
这几个地址都定义在arch/arm/include/asm/memory.h，我们来稍微分析下这个头文件，首先它包含了arch/memory.h，我们来看看arch/arm/mach-msm/include/mach/memory.h，在这个里面定义了define PHYS_OFFSET     UL(0x00200000) 这个实际上是memory的物理内存初始地址，这个地址和我们以前在boardconfig.h里面定义的是一致的。然后我们再看asm/memory.h，他里面定义了我们的memory虚拟地址的首地址define PAGE_OFFSET     UL(CONFIG_PAGE_OFFSET)。  
另外我们在head.S里面看到kernel的物理或者虚拟地址的定义都有一个偏移，这个偏移又是从哪来的呢，实际我们可以从arch/arm/Makefile里面找到：textofs-y   := 0x00008000     TEXT_OFFSET := $(textofs-y) 这样我们再看kernel启动时候的物理地址和链接地址，实际上它和我们前面在boardconfig.h和Makefile.boot里面定义的都是一致的～
建立初始化页表以后，会首先将__switch_data这个symbol的链接地址放在sp里面，然后获得__enable_mmu的物理地址，然后会跳到__proc_info_list里面的INITFUNC执行，这个偏移是定义在arch/arm/kernel/asm-offset.c里面，实际上就是取得__proc_info_list里面的__cpu_flush这个函数执行。
```  
ldr r13, __switch_data @ address to jump to after 
@ mmu has been enabled 
adr lr, __enable_mmu @ return (PIC) address 
add pc, r10, PROCINFO_INITFUNC 
```
这个__cpu_flush在这里就是我们proc-v6.S里面的__v6_setup函数了，具体它的实现我就不分析了，都是对arm控制寄存器的操作，这里转一下它对这部分操作的注释，看完之后就基本知道它完成的功能了。
```  
[Tags]/*
[Tags]* __v6_setup
[Tags]*
[Tags]* Initialise TLB, Caches, and MMU state ready to switch the MMU
[Tags]* on. Return in r0 the new CP15 C1 control register setting.
[Tags]*
[Tags]* We automatically detect if we have a Harvard cache, and use the
[Tags]* Harvard cache control instructions insead of the unified cache
[Tags]* control instructions.
[Tags]*
[Tags]* This should be able to cover all ARMv6 cores.
[Tags]*
[Tags]* It is assumed that: 
[Tags]* - cache type register is implemented
[Tags]*/ 
```
完成这部分关于CPU的操作以后，下面就是打开MMU了，这部分内容也没什么好说的，也是对arm控制寄存器的操作，打开MMU以后我们就可以使用虚拟地址了，而不需要我们自己来进行地址的重定位，ARM硬件会完成这部分的工作。打开MMU以后，会将SP的值赋给PC，这样代码就会跳到__switch_data来运行，这个__switch_data是一个定义在head-common.S里面的结构体，我们实际上是跳到它地一个函数指针__mmap_switched处执行的。
这个switch的执行过程我们只是简单看一下，前面的copy data_loc段以及清空.bss段就不用说了，它后面会将proc的信息和machine的信息保存在__switch_data这个结构体里面，而这个结构体将来会在start_kernel的setup_arch里面被使用到。这个在后面的对start_kernel的详细分析中会讲到。另外这个switch还涉及到控制寄存器的一些操作，这里我不没仔细研究spec，不懂也就不说了～
好啦，switch操作完成以后就会b start_kernel了～ 这样就进入了c代码的运行了～