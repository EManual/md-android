在这个例子中没有说到模拟SD CARD，所以这个补充对我们这些菜鸟朋友来说应该有点用处。
模拟器或真机都会有一定大小的内部存储空间（不是指内存，指的是持久化存储空间），但这并不够，有时我们需要更大的存储空间。为了在模拟器上开发使用扩展存储空间的程序，需要在PC上模拟一个SDCard的虚拟文件，然后加载到模拟器中。
sdcard文件使用tools目录下的mksdcard.exe命令生成，假设要生成大小256M的sdcard文件，可以使用如下的命令：
```  
mksdcard -l mycard 256M sdcard/mycard.img 
```
使用mksdcard命令要注意如下六点：
1. mycard命令可以使用三种尺寸：字节、K和M。如果只使用数字，表示字节。后面还可以跟K，如262144K，也表示256M。
2. mycard建立的虚拟文件最小为8M，也就是说，模拟器只支持大于8M的虚拟文件。
3. -l命令行参数表示虚拟磁盘的卷标，可以没有该参数。
4. 虚拟文件的扩展名可以是任意的，如mycard.abc。
5. mksdcard命令不会自动建立不存在的目录，因此，在执行上面命令之前，要先在当前目录(tools目录)中建立一个sdcard目录。
6. mksdcard命令是按实际大小生成的sdcard虚拟文件。也就是说，生成256M的虚拟文件的尺寸就是256M。
执行完上面的命令后，在tools/sdcard目录下会生成mycard.img文件。
在Eclipse中新建一个AVD，注意SD Card选项选择刚刚创建的mycard.img虚拟文件。
创建好AVD后，打开run dialog切换到Target选项选择刚刚创建的AVD
启动模拟器，这时模拟器便自动加载了我们配置的SD CARD，起来之后在sdcard目录下会生成一个名字为mycard.img.lock的临时文件夹，如果没有生成则说明SD CARD加载失败。
SD CARD加载成功后通过什么样的方式来管理呢？比如我要向SD CARD中添加一个文件，这里可以通过多种方式来实现：
1、使用命令 ：adb push C:/video. 3gp sdcard 
2、在eclipse中进行管理，打开DDMS透视图，通过右上角的两个按钮可以添加、删除文件。
以上的步骤操作成功后便可以将视频文件放到SD CARD中。