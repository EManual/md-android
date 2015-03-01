如测试程序、自主开发的APK或第三方的APK，这样我们就有必要把这些APK程序打包到文件系统里编译生成镜像了，具体操作如下：
(1)源码编译后，把apk拷贝到out/target/product/generic/system/app中。 
(2) 执行命令make snod, 把添加的spk编到system.img中 
缺点：执行make clean 后，再次make 完毕需要重新执行上面操作。
"方法一"的改进。 
(1) 新建一个文件夹目录，用来存放apk文件 
```  
mkdir packages/apps/Prebuilt_apps 
cd packages/apps/Prebuilt_apps 
```
在Prebuilt_apps中新建make文件 
vi Android.mk 
并写入 
```  
LOCAL_PATH := $(call my-dir) 
include $(CLEAR_VARS) 
LOCAL_POST_PROCESS_COMMAND := $(shell cp -r $(LOCAL_PATH)/*.apk $(TARGET_OUT)/app/) 
```
保存退出。
(2) 把需要编译的apk拷贝到目录Prebuilt_apps下，执行make ，Prebuilt_apps中的apk就会考被到out/target/product/generic/system/app中。
(3) 执行make snod 。完成。
此方法执行make clean 后，再次make 完毕，只需要make snod即可(有时make后，out/target/product/generic/system/app没有需要添加的apk，此时再make一次即可，速度很快)。