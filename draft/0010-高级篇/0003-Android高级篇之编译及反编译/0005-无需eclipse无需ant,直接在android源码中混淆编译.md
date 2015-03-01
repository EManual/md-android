终于知道怎么在android源码中混淆编译了，不用ant也不用eclipse插件。
在需要混淆的工程目录下(package/apps/下的工程)添加proguard.flags文件，该文件即为网络传说中的proguard.cfg，只是命名不一样而已，然后再Android.mk中添加如下两句：
```  
LOCAL_PROGUARD_ENABLED := full
LOCAL_PROGUARD_FLAG_FILES := proguard.flags
```
上面的full 也可以是custom，如果不写这句，那还得添加如下一句：
```  
TARGET_BUILD_VARIANT := user或者TARGET_BUILD_VARIANT := userdebug
```
这样后在工程目录下执行mm便可以看到在out目录下生成了形如proguard.classes.jar的东东，这就说明已在编译中启动了proguard。
但反编译一看，并未出现网络云说的abcd替代符号，其实代码并未真正混淆。
android在编译时默认关闭了混淆选项，有去研究build/core目录的同志会发现这里也有个proguard.flags文件，其实在proguard的过程中，编译器会调用包括本地目录下和系统定义了的多个proguard.flags文件，而在这个文件中混淆的选项被禁止了，故而编译出来的apk仍未混淆。因此将如下句子注释掉便可实现真正的混淆编译。
```  
 Don't obfuscate. We only need dead code striping.
```
-dontobfuscate（将该句加个号注释掉）
好奇的同志还可以继续看看，为什么TARGET_BUILD_VARIANT := user和LOCAL_PROGUARD_ENABLED := full二选一即可，详见build/core/package.mk。
```  
LOCAL_PROGUARD_ENABLED:=$(strip $(LOCAL_PROGUARD_ENABLED))
ifndef LOCAL_PROGUARD_ENABLED
ifneq ($(filter user userdebug, $(TARGET_BUILD_VARIANT)),)
 turn on Proguard by default for user & userdebug build
LOCAL_PROGUARD_ENABLED :=full
endif
endif
ifeq ($(LOCAL_PROGUARD_ENABLED),disabled)
 the package explicitly request to disable proguard.
LOCAL_PROGUARD_ENABLED :=
endif
proguard_options_file :=
ifneq ($(LOCAL_PROGUARD_ENABLED),custom)
ifneq ($(all_resources),)
proguard_options_file := $(package_expected_intermediates_COMMON)/proguard_options
endif  all_resources
endif  !custom
LOCAL_PROGUARD_FLAGS := $(addprefix -include ,$(proguard_options_file)) $(LOCAL_PROGUARD_FLAGS)
```
具体我就不解释了，大家自己理解吧哈。