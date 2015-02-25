3.编辑.c文件实现native方法。
com_hello_jnitest_Nadd.c文件：
```  
include <stdlib.h>
include "com_hello_jnitest_Nadd.h"
JNIEXPORT jint JNICALL Java_com_hello_jnitest_Nadd_nadd(JNIEnv * env, jobject c, jint a, jint b)
{
	return (a+b);
}
```
4.编译.c文件生存动态库。
```  
arm-none-linux-gnueabi-gcc -I/home/a/work/android/jdk1.6.0_17/include -I/home/a/work/android/jdk1.6.0_17/include/linux -fpic -c com_hello_jnitest_Nadd.c
arm-none-linux-gnueabi-ld -T/home/a/CodeSourcery/Sourcery_G++_Lite/arm-none-linux-gnueabi/lib/ldscripts/armelf_linux_eabi.xsc -share -o libNadd.so com_hello_jnitest_Nadd.o
```
得到libNadd.so文件。
以上在ubuntu中完成。
5.将相应的动态库文件push到avd的system/lib中:adb push libNadd.so /system/lib。若提示Read-only file system错误，运行adb remount命令，即可。
```  
Adb push libNadd.so /system/lib
```
6.在eclipsh中运行原应用程序即可。以上在windows中完成。对于一中生成的so文件也可采用二中的方法编译进apk包中。只需在工程文件夹中建libs\armeabi文件夹（其他文件夹名无效，只建立libs文件夹也无效），然后将so文件拷入，编译工程即可。