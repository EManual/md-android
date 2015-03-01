首先启动android模拟器。
打开cmd命令行窗口。
```  
输入adb -s emulator-5554 shell
```
此时可以管理系统文件夹了，再输入ls
可以看到列出了文件夹和文件，输入cd system/app再输入ls
可以看到系统自带的应用程序apk文件，删除你想要删除的，例如Phone.apk，输入rm Phone.apk
此时会看到提示
```  
rm failed for Phone.apk, Read-only file system
```
那是因为这些是只读文件，我们没有权限删除它。所以接下来要做的是获取权限，首先查看权限，输入mount
可以看到/dev/block/mtdblock0 /system yaffs2 ro 0 0说明在system这个地方我们没有权限那么接下来我们就来获取权限，输入
```  
mount -o remount,rw -t yaffs2 /dev/block/mtdblock0 /system
```
没有提示错误，再次查看权限，输入mount
可以看到/dev/block/mtdblock0 /system yaffs2 rw 0 0
说明我们已经获取到权限了此时再输入rm Phone.apk就可以成功删除了
最后一点，就算你成功删除了，android模拟器每次启动时也会恢复回来。
那么如何永久删除呢，很简单，删除SdkSetup.apk，输入rm SdkSetup.apk
还没完，找到avd目录(一般在我的文档)，进入xxxx.avd目录，删除cache.img和userdata-qemu.img
还有还有，找到%SDK_HOME%/platforms/android-X/images/system.img，复制到上面的目录中。
最后最后，再重启模拟器，大功告成！