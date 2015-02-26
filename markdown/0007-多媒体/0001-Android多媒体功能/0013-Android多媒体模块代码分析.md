最近一直在研究Android的多媒体模块，一直想总结一下学习的成果，所以想写一下自己的对Android多媒体部分的理解，欢迎各位一起来讨论。
我一直在想用什么样的方法能够详细的分析android源码，想按照经典著作《Linux源码分析》中按照功能对代码进行一个遍历。不足之处，欢迎指正。
像刚开始学编程，来一个helloworld一样，这里先给出一个使用MediaRecorder的例子：
这个例子为了更方便的学习，去除了Android 应用程序的框架，仅仅显示MediaRecorder的调用方法。
```  
mMediaRecorder01 = new MediaRecorder();
mMediaRecorder01.setAudioSource(MediaRecorder.AudioSource.DEFAULT);
mMediaRecorder01.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
mMediaRecorder01.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
mMediaRecorder01.setOutputFile(“输出文件+路径”);
mMediaRecorder01.prepare();
mMediaRecorder01.start();
//录音中。。。。。。
mMediaRecorder01.stop();
mMediaRecorder01.reset();
mMediaRecorder01.release();
mMediaRecorder01 = null;
```
我们来深入分析一下这个例子，一句一句的分析，首先
```  
mMediaRecorder01 = new MediaRecorder();
```
这是个普通Java语句，用来创建一个MediaRecorder对象，然后调用MediaRecorder构造函数，来看一下MediaRecorder构造函数
```  
public MediaRecorder() {
		Looper looper;
		if ((looper = Looper.myLooper()) != null) {
			mEventHandler = new EventHandler(this, looper);
		} else if ((looper = Looper.getMainLooper()) != null) {
			mEventHandler = new EventHandler(this, looper);
		} else {
			mEventHandler = null;
		}
		/*
		  * Native setup requires a weak reference to our object. It's easier to
		  * create it here than in C++.
		  */
		native_setup(new WeakReference<MediaRecorder>(this));
	}
```
这个构造函数主要是来初始化成员变量mEventHandler, EventHandler 是Handler的子类，用于处理消息事件。
这个构造函数位于framework/base/media/java/android/media/mediarecorder.java中
在mediarecorder.java文件中
```  
public class MediaRecorder {
	static {
		System.loadLibrary("media_jni");
		native_init();
	}
}
```
这个是使用JNI典型特征，用来来调用C/C++库media_jni.so中的函数
往下走
```  
mMediaRecorder01.setAudioSource(MediaRecorder.AudioSource.DEFAULT);
	|
	|-> public native void setAudioSource(int audio_source)  <mediarecorder.java>
		| //JNI 调用
		|->android_media_MediaRecorder_setAudioSource(JNIEnv *env, jobject thiz, jint as)  <android_media_MediaRecorder.cpp>
```
在android_media_MediaRecorder_setAudioSource(JNIEnv *env, jobject thiz, jint as) 中主要下面的语句
```  
sp<MediaRecorder> mr = getMediaRecorder(env, thiz);  //获取MediaRecorder对象mr， 
process_media_recorder_call(env, mr->setAudioSource(as), "java/lang/RuntimeException", "setAudioSource failed.");//通过mr调用setAudioSource成员函数
```  
这里的MediaRecorder 是C++对象，代码位于MediaRecorder.h,MediaRecorder.cpp中(代码路径为framework/base/include/media, framework/base/media/libmedia)
继续往下走
```  
android_media_MediaRecorder_setAudioSource(JNIEnv *env, jobject thiz, jint as)  <android_media_MediaRecorder.cpp>
	|
	|->MediaRecorder::setAudioSource(int as) <mediarecorder.cpp>
```
在MediaRecorder::setAudioSource(int as) 中发现
```  
mMediaRecorder->setAudioSource(as);
```