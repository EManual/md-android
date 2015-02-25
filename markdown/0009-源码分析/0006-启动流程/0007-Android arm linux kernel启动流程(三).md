这里首先判断LC0当前的运行地址和链接地址是否一样，如果一样就不需要重定位，如果不一样则需要进行重定位。这里肯定是不相等的，因为我们可以通过objdump看到LC0的地址是0x00000138，是一个相对地址，然后adr r0, LC0 
实际上就是将LC0当前的运行地址，而我们直接跳到ZTEXTADDR跑的，实际上PC里面现在的地址肯定是0x00208000以后的一个值，adr r0, LC0编译之后实际上为add r0, pc, 208，这个208就是LC0到.text段头部的偏移。
```  
01.add r5, r5, r0 
02.add r6, r6, r0 
03.add ip, ip, r0
```
然后就是重定位了，即都加上一个偏移，经过重定位以后就都是绝对地址了。
```  
not_relocated: mov r0, 0 
1: str r0, [r2], 4 @ clear bss 
str r0, [r2], 4 
str r0, [r2], 4 
str r0, [r2], 4 
cmp r2, r3 
blo 1b 
[Tags]/* 
[Tags]* The C runtime environment should now be setup 
[Tags]* sufficiently. Turn the cache on, set up some 
[Tags]* pointers, and start decompressing. 
[Tags]*/ 
bl cache_on 
```
重定位完成以后打开cache，具体这个打开cache的过程咱没仔细研究过，大致过程是先从C0里面读到processor ID，然后根据ID来进行cache_on。
```  
mov r1, sp @ malloc space above stack 
add r2, sp, 0x10000 @ 64k max 
```
解压的过程首先是在堆栈之上申请一个空间
```  
[Tags]/* 
[Tags]* Check to see if we will overwrite ourselves. 
[Tags]* r4 = final kernel address 
[Tags]* r5 = start of this image 
[Tags]* r2 = end of malloc space (and therefore this image) 
[Tags]* We basically want: 
[Tags]* r4 >= r2 -> OK 
[Tags]* r4 + image length <= r5 -> OK 
[Tags]*/
cmp r4, r2 
bhs wont_overwrite 
sub r3, sp, r5 @ > compressed kernel size 
add r0, r4, r3, lsl 2 @ allow for 4x expansion 
cmp r0, r5 
bls wont_overwrite 
mov r5, r2 @ decompress after malloc space 
mov r0, r5 
mov r3, r7 
bl decompress_kernel 
add r0, r0, 127 + 128 @ alignment + stack 
bic r0, r0, 127 @ align the kernel length 
```
这个过程是判断我们解压出的vmlinx会不会覆盖原来的zImage，这里的final kernel address就是解压后的kernel要存放的地址，而start of this image则是zImage在内存中的地址。根据我们前面的分析，现在这两个地址是重复的，即都是0x00208000。同样r2是我们申请的一段内存空间，因为他是在sp上申请的，而根据vmlinx.lds我们知道stack实际上处与vmlinx的最上面，所以r4>=r2是不可能的，这里首先计算zImage的大小，然后判断r4+r3是不是比r5小，很明显r4和r5的值是一样的，所以这里先将r2的值赋给r0，经kernel先解压到s申请的内存空间上面，具体的解压过程就不描述了，定义在misc.c里面。
```  
[Tags]/* r0 = decompressed kernel length 
[Tags]* r1-r3 = unused 
[Tags]* r4 = kernel execution address 
[Tags]* r5 = decompressed kernel start 
[Tags]* r6 = processor ID 
[Tags]* r7 = architecture ID 
[Tags]* r8 = atags pointer 
[Tags]* r9-r14 = corrupted 
[Tags]*/ 
add r1, r5, r0 @ end of decompressed kernel 
adr r2, reloc_start 
ldr r3, LC1 
add r3, r2, r3 
ldmia r2!, {r9 - r14} @ copy relocation code 
stmia r1!, {r9 - r14} 
ldmia r2!, {r9 - r14} 
stmia r1!, {r9 - r14} 
cmp r2, r3 
blo 1b 
add sp, r1, 128 @ relocate the stack 
bl cache_clean_flush 
add pc, r5, r0 @ call relocation code 
```
因为没有将kernel解压在要求的地址，所以必须重定向，说穿了就是要将解压的kernel拷贝到正确的地址，因为正确的地址与zImage的地址是重合的，而要拷贝我们又要执行zImage的重定位代码，所以这里首先将重定位代码reloc_start拷贝到vmlinx上面，然后再将vmlinx拷贝到正确的地址并覆盖掉zImage。这里首先计算出解压后的vmlinux的高地址放在r1里面，r2存放着重定位代码的首地址，r3存放着重定位代码的size，这样通过拷贝就将reloc_start移动到vmlinx后面去了，然后跳转到重定位代码开始执行。
```  
[Tags]/* 
[Tags]* All code following this line is relocatable. It is relocated by 
[Tags]* the above code to the end of the decompressed kernel image and 
[Tags]* executed there. During this time, we have no stacks. 
[Tags]* 
[Tags]* r0 = decompressed kernel length 
[Tags]* r1-r3 = unused 
[Tags]* r4 = kernel execution address 
[Tags]* r5 = decompressed kernel start 
[Tags]* r6 = processor ID 
[Tags]* r7 = architecture ID 
[Tags]* r8 = atags pointer 
[Tags]* r9-r14 = corrupted 
[Tags]*/ 
.align 5 
reloc_start: add r9, r5, r0 
sub r9, r9, 128 @ do not copy the stack 
debug_reloc_start 
mov r1, r4 
1: 
.rept 4 
ldmia r5!, {r0, r2, r3, r10 - r14} @ relocate kernel 
stmia r1!, {r0, r2, r3, r10 - r14} 
.endr 
cmp r5, r9 
blo 1b 
add sp, r1, 128 @ relocate the stack 
debug_reloc_end 
call_kernel: bl cache_clean_flush 
bl cache_off 
mov r0, 0 @ must be zero 
mov r1, r7 @ restore architecture number 
mov r2, r8 @ restore atags pointer 
mov pc, r4 @ call kernel 
```
这里就是将vmlinx拷贝到正确的地址了，拷贝到正确的位置以后，就将kernel的首地址赋给PC，然后就跳转到真正kernel启动的过程～～
最后我们来总结一下一个基本的过程：
1）当bootloader要从分区中数据读到内存中来的时候，这里涉及最重要的两个地址，一个就是ZTEXTADDR还有一个是INITRD_PHYS。不管用什么方式来生成IMG都要让bootloader有方法知道这些参数，不然就不知道应该将数据从FLASH读入以后放在什么地方，下一步也不知道从哪个地方开始执行了；
2）bootloader将IMG载入RAM以后，并跳到zImage的地址开始解压的时候，这里就涉及到另外一个重要的参数，那就是ZRELADDR，就是解压后的kernel应该放在哪。这个参数一般都是arch/arm/mach-xxx下面的Makefile.boot来提供的；
3）另外现在解压的代码head.S和misc.c一般都会以PIC的方式来编译，这样载入RAM在任何地方都可以运行，这里涉及到两次冲定位的过程，基本上这个重定位的过程在ARM上都是差不多一样的。