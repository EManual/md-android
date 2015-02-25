Android 开机会出现3个画面： 
1. Linux 系统启动，出现Linux小企鹅画面(reboot)(Android 1.5及以上版本已经取消加载图片)； 
2. Android平台启动初始化，出现"A N D R I O D"文字字样画面； 
3. Android平台图形系统启动，出现含闪动的ANDROID字样的动画图片(start)。 
#### 1、开机图片(Linux小企鹅) (Android 1.5及以上版本已经取消加载图片)； 
Linux Kernel引导启动后，加载该图片。 
logo.c中定义nologo,在fb_find_logo(int depth)函数中根据nologo的值判断是否需要加载相应图片。 
代码如下： 
```  
static int nologo; 
module_param(nologo, bool, 0); 
MODULE_PARM_DESC(nologo, "Disables startup logo"); 
/* logo's are marked __initdata. Use __init_refok to tell 
[Tags]* modpost that it is intended that this function uses data 
[Tags]* marked __initdata. 
[Tags]*/ 
const struct linux_logo * __init_refok fb_find_logo(int depth) 
{ 
	const struct linux_logo *logo = NULL;
	if (nologo) 
	return NULL; 
	......
} 
```
相关代码： 
```  
/kernel/drivers/video/fbmem.c 
/kernel/drivers/video/logo/logo.c 
/kernel/drivers/video/logo/Kconfig 
/kernel/include/linux/linux_logo.h 
```
#### 2、开机文字("A N D R I O D") 
Android 系统启动后，init.c中main()调用load_565rle_image()函数读取/initlogo.rle（一张565 rle压缩的位图），如果读取成功，则在/dev/graphics/fb0显示Logo图片；如果读取失败，则将/dev/tty0设为TEXT模式， 并打开/dev/tty0，输出文本“A N D R I O D”字样。 
定义加载图片文件名称 
```  
//=============Android.mk====================== 
LOCAL_PATH:= $(call my-dir) 
include $(CLEAR_VARS) 
LOCAL_SRC_FILES:= \ 
bootanimation_main.cpp \ 
BootAnimation.cpp 
 need "-lrt" on Linux simulator to pick up clock_gettime 
ifeq ($(TARGET_SIMULATOR),true) 
ifeq ($(HOST_OS),linux) 
LOCAL_LDLIBS += -lrt 
endif 
endif 
LOCAL_SHARED_LIBRARIES := \ 
libcutils \ 
libutils \ 
libui \ 
libcorecg \ 
libsgl \ 
libEGL \ 
libGLESv1_CM \ 
libmedia   
LOCAL_C_INCLUDES := \ 
$(call include-path-for, corecg graphics) 
LOCAL_MODULE:= bootanimation 
include $(BUILD_EXECUTABLE) 
//========================================== 
```
相关代码： 
```  
/system/core/init/init.c  
/system/core/init/init.h 
/system/core/init/init.rc 
/system/core/init/logo.c 
```
*.rle文件的制作步骤: 
a. 使用GIMP或者Advanced Batch Converter软件，将图象转换为RAW格式； 
b. 使用android自带的rgb2565工具，将RAW格式文件转换为RLE格式(如：rgb2565 -rle < initlogo.raw > initlogo.rle)。 
#### 3、开机动画(闪动的ANDROID字样的动画图片) 
Android 1.5版本：Android的系统登录动画类似于Windows系统的滚动条，是由前景和背景两张PNG图片组成，这两张图片存在于手机或模拟器 /system/framework /framework-res.apk文件当中，对应原文件位于/frameworks/base/core/res/assets/images/。前 景图片（android-logo-mask.png）上的Android文字部分镂空，背景图片（android-logo-shine.png）则是 简单的纹理。系统登录时，前景图片在最上层显示，程序代码（BootAnimation.android()）控制背景图片连续滚动，透过前景图片文字镂 空部分滚动显示背景纹理，从而实现动画效果。 
相关代码： 
```  
/frameworks/base/libs/surfaceflinger/BootAnimation.h 
/frameworks/base/libs/surfaceflinger/BootAnimation.cpp 
/frameworks/base/core/res/assets/images/android-logo-mask.png  Android默认的前景图片，文字部分镂空，大小256×64 
/frameworks/base/core/res/assets/images/android-logo-shine.png Android默认的背景图片，有动感效果，大小512×64 
```
Android 1.6及以上版本： 
init.c解析init.rc（其中定义服务：“service bootanim /system/bin/bootanimation”），bootanim 服务由SurfaceFlinger.readyToRun()（property_set("ctl.start", "bootanim");）执行开机动画、bootFinished()（property_set("ctl.stop", "bootanim");）执行停止开机动画。 
BootAnimation.h和BootAnimation.cpp文件放到了/frameworks/base/cmds /bootanimation目录下了，增加了一个入口文件bootanimation_main.cpp。Android.mk文件中可以看到，将开机 动画从原来的SurfaceFlinger里提取出来了，生成可执行文件：bootanimation。Android.mk代码如下： 
```  
//=============Android.mk====================== 
LOCAL_PATH:= $(call my-dir) 
include $(CLEAR_VARS) 
LOCAL_SRC_FILES:= \ 
bootanimation_main.cpp \ 
BootAnimation.cpp 
 need "-lrt" on Linux simulator to pick up clock_gettime 
ifeq ($(TARGET_SIMULATOR),true) 
ifeq ($(HOST_OS),linux) 
LOCAL_LDLIBS += -lrt 
endif 
endif 
LOCAL_SHARED_LIBRARIES := \ 
libcutils \ 
libutils \ 
libui \ 
libcorecg \ 
libsgl \ 
libEGL \ 
libGLESv1_CM \ 
libmedia   
LOCAL_C_INCLUDES := \ 
$(call include-path-for, corecg graphics) 
LOCAL_MODULE:= bootanimation 
include $(BUILD_EXECUTABLE) 
//========================================== 
```
（1）adb shell后，可以直接运行“bootanimation”来重新看开机动画，它会一直处于动画状态，而不会停止。 
（2）adb shell后，命令“setprop ctl.start bootanim”执行开机动画；命令“getprop ctl.start bootanim”停止开机动画。这两句命令分别对应SurfaceFlinger.cpp的两句语 句：property_set("ctl.start", "bootanim");和property_set("ctl.stop", "bootanim"); 
相关文件：
```  
/frameworks/base/cmds/bootanimation/BootAnimation.h 
/frameworks/base/cmds/bootanimation/BootAnimation.cpp 
/frameworks/base/cmds/bootanimation/bootanimation_main.cpp 
/system/core/init/init.c 
/system/core/rootdir/init.rc 
```