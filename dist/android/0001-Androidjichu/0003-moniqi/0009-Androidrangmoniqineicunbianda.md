今天我们就来说说模拟器的内存，有的时候我们在用模拟器开发应用的时候，会用到内存，那么一般的应用的话，模拟器自带的内存就够用了，但是有的时候我们会接触到很大的应用的话，我们模拟器上的内存就会不够用，那么我们怎么办才行呢，大家到了这里以后就会头大，过了今天以后童鞋们在看到这种情况的话，就不会再头大了，那么我们还等什么，让自己的android模拟器内存变的更大一些吧。
1. 我们以Windows平台的SDK为例，这里模拟器配置路径为 C:\Documents and Settings\android\.android\avd\android3.avd，我们用记事本打开这个ini文件，当然我们可以看到Unix/Binary的换行符，这里建议你使用UltraEdit或Notepad++打开，这里仅作为演示我们大家说下这个文件的结构吧，
```  
hw.lcd.density=160 ：是屏幕的密度
sdcard.size=64M ：这句代表分配SD卡的大小，我这里仅给了64M
skin.name=WXGA ：分辨率
skin.path=platforms\android-11\skins\WXGA ：模拟器皮肤
hw.keyboard.lid=no ：是否有物理键盘
vm.heapSize=48 ：虚拟机默认堆大小
hw.ramSize=256 ：模拟器的RAM运行内存大小，可以看到这里只有可怜的256MB
image.sysdir.1=platforms\android-11\images\ ：模拟器的映像文件路径
```
这里大家主要是修改hw.ramSize这句，将后面的256换为更大的，当然要根据你PC电脑的物理内存来修改了，否则会严重映像你电脑的性能，如果你电脑的内存是2GB或以上，推荐和摩托Xoom平台的RAM设置的一样大小，hw.ramSize=后面写1024，保存即可，如图：

![img](http://emanual.github.io/md-android/img/basic_emulator/10_emulator2.jpg) 