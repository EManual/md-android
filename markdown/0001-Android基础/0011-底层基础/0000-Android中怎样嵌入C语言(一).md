#### Android JNI知识简介
Java Native Interface (JNI)标准是java平台的一部分，它允许Java代码和其他语言写的代码进行交互。JNI 是本地编程接口，它使得在 Java 虚拟机 (VM) 内部运行的 Java 代码能够与用其它编程语言(如 C、C++ 和汇编语言)编写的应用程序和库进行交互操作。
#### 1.从如何载入.so档案谈起
由于Android的应用层的类都是以Java写的，这些Java类编译为Dex型式的Bytecode之后，必须靠Dalvik虚拟机(VM: Virtual Machine)来执行。VM在Android平台里，扮演很重要的角色。
此外，在执行Java类的过程中，如果Java类需要与C组件沟通时，VM就会去载入C组件，然后让Java的函数顺利地调用到C组件的函数。此时，VM扮演着桥梁的角色，让Java与C组件能通过标准的JNI介面而相互沟通。
应用层的Java类是在虚拟机(VM: Vitual Machine)上执行的，而C件不是在VM上执行，那么Java程式又如何要求VM去载入(Load)所指定的C组件呢? 可使用下述指令：
System.loadLibrary(*.so的档案名); 
例如，Android框架里所提供的MediaPlayer.java类，含指令：
```  
public class MediaPlayer{ 
	static {
		System.loadLibrary("media_jni");
	}
}
```
这要求VM去载入Android的/system/lib/libmedia_jni.so档案。载入*.so之后，Java类与*.so档案就汇合起来，一起执行了。
#### 2.如何撰写*.so的入口函数
JNI_OnLoad()与JNI_OnUnload()函数的用途
当Android的VM(Virtual Machine)执行到System.loadLibrary()函数时，首先会去执行C组件里的JNI_OnLoad()函数。它的用途有二：
(1)告诉VM此C组件使用那一个JNI版本。如果你的*.so档没有提供JNI_OnLoad()函数，VM会默认该*.so档是使用最老的JNI 1.1版本。由于新版的JNI做了许多扩充，如果需要使用JNI的新版功能，例如JNI 1.4的java.nio.ByteBuffer,就必须藉由JNI_OnLoad()函数来告知VM。
(2)由于VM执行到System.loadLibrary()函数时，就会立即先呼叫JNI_OnLoad()，所以C组件的开发者可以藉由JNI_OnLoad()来进行C组件内的初期值之设定(Initialization)。
例如，在Android的/system/lib/libmedia_jni.so档案里，就提供了JNI_OnLoad()函数，其程式码片段为：
```  
//define LOG_NDEBUG 0
define LOG_TAG "MediaPlayer-JNI
jint JNI_OnLoad(JavaVM* vm, void* reserved)
{
	JNIEnv* env = NULL;
	jint result = -1;
	if (vm->GetEnv((void**) &env, JNI_VERSION_1_4) != JNI_OK) {
		LOGE("ERROR: GetEnv failed\n");
		goto bail;
	}
	assert(env != NULL);
	if (register_android_media_MediaPlayer(env) < 0) {
		LOGE("ERROR: MediaPlayer native registration failed\n");
		goto bail;
	}
	if (register_android_media_MediaRecorder(env) < 0) {
		LOGE("ERROR: MediaRecorder native registration failed\n");
		goto bail;
	}
	if (register_android_media_MediaScanner(env) < 0) {
		LOGE("ERROR: MediaScanner native registration failed\n");
		goto bail;
	}
	if (register_android_media_MediaMetadataRetriever(env) < 0) {
		LOGE("ERROR: MediaMetadataRetriever native registration failed\n");
		goto bail;
	}
	/* success -- return valid version number */
	result = JNI_VERSION_1_4;
	bail:
		return result;
}
```