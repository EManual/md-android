二.使用NDK生成本地方法（ubuntu and windows）
1.安装NDK：解压，然后进入NDK解压后的目录，运行build/host-setup.sh（需要Make3.81和awk）。若有错，修改host-setup.sh文件：将!/bin/sh修改为!/bin/bash，再次运行即可。
2.在apps文件夹下建立自己的工程文件夹，然后在该文件夹下建一文件Application.mk和项project文件夹。
Application.mk文件：
```  
APP_PROJECT_PATH := $(call my-dir)/project
APP_MODULES      := myjni
```
3.在project文件夹下建一jni文件夹，然后新建Android.mk和myjni.c。这里不需要用javah生成相应的.h文件，但函数名要包含相应的完整的包、类名。
4.编辑相应文件内容。
Android.mk文件：
```  
java代码：
 Copyright (C) 2009 The Android Open Source Project

 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at

 http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.

LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := myjni
LOCAL_SRC_FILES := myjni.c
include $(BUILD_SHARED_LIBRARY)
```
myjni.c文件：
```  
include <string.h>
include <jni.h>
jstring
Java_com_hello_NdkTest_NdkTest_stringFromJNI( JNIEnv* env,
jobject thiz )
{
	return (*env)->NewStringUTF(env, "Hello from My-JNI !");
}
```
myjni文件组织：
```  
a@ubuntu:~/work/android/ndk-1.6_r1/apps$ tree myjni
myjni
|-- Application.mk
`-- project
|-- jni
| |-- Android.mk
| `-- myjni.c
`-- libs
`-- armeabi
`-- libmyjni.so
```
4 directories, 4 files
5.编译：make APP=myjni.
以上内容在ubuntu完成。以下内容在windows中完成。当然也可以在ubuntu中完成。
6.在eclipsh中创建android application。将myjni中自动生成的libs文件夹拷贝到当前工程文件夹中，编译运行即可。
NdkTest.java文件：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;
public class NdkTest extends Activity {
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		TextView tv = new TextView(this);
		tv.setText(stringFromJNI());
		setContentView(tv);
	}
	public native String stringFromJNI();
	static {
		System.loadLibrary("myjni");
	}
}
```