#### Audio系统的JNI代码
Android的Audio部分通过JNI向Java层提供接口，在Java层可以通过JNI接口完成Audio系统的大部分操作。
Audio JNI部分的代码路径为：frameworks/base/core/jni。
其中，主要实现的3个文件为：android_media_AudioSystem.cpp、android_media_Audio Track.cpp和android_media_AudioRecord.cpp，它们分别对应了Android Java框架中的3个类的支持：
```  
android.media.AudioSystem：负责Audio系统的总体控制；
android.media.AudioTrack：负责Audio系统的输出环节；
android.media.AudioRecorder：负责Audio系统的输入环节。
```
在Android的Java层中，可以对Audio系统进行控制和数据流操作，对于控制操作，和底层的处理基本一致；但是对于数据流操作，由于Java不支持指针，因此接口被封装成了另外的形式。
例如，对于音频输出，android_media_AudioTrack.cpp提供的是写字节和写短整型的接口类型。
```  
static jint android_media_AudioTrack_native_write(JNIEnv *env,  jobject thiz, jbyteArray javaAudioData,  
                 jint offsetInBytes, jint sizeInBytes,  jint javaAudioFormat) {
     jbyte* cAudioData = NULL;
     AudioTrack *lpTrack = NULL;
     lpTrack = (AudioTrack *)env->GetIntField(this, javaAudioTrackFields. Native TrackInJavaObj);
     // …… 省略部分内容
     ssize_t written = 0;
     if (lpTrack->sharedBuffer() == 0) {  
          //进行写操作
          written = lpTrack->write(cAudioData + offsetInBytes, sizeInBytes);
     } else {  
          if (javaAudioFormat == javaAudioTrackFields.PCM16) {  
               memcpy(lpTrack->sharedBuffer()->pointer(),  cAudioData+offsetInBytes, sizeInBytes);
               written = sizeInBytes;
          } else{
               if (javaAudioFormat == javaAudioTrackFields.PCM8) {  
                    int count = sizeInBytes;  
                    int16_t *dst = (int16_t *)lpTrack->sharedBuffer()->pointer();
                    const int8_t *src = (const int8_t *)(cAudioData + offsetInBytes);
                    while(count--) {
                         [Tags]*dst++ = (int16_t)([Tags]*src++^0x80) << 8;  
                     }  
                    written = sizeInBytes;
                }  
          }  

     // …… 省略部分内容
     env->ReleasePrimitiveArrayCritical(javaAudioData, cAudioData, 0);
     return (int)written;  
}
```
所定义的JNI接口native_write_byte和native_write_short如下所示：
```  
{"native_write_byte", "([BIII)I", (void *)android_media_AudioTrack_native_write},  
{"native_write_short", "([SIII)I", (void *)android_media_AudioTrack_native_ write_short},
```
向Java提供native_write_byte和native_write_short接口，它们一般是通过调用AudioTrack的write()函数来完成的，只是在Java的数据类型和C++的指针中做了一步转换。
#### Audio系统的Java代码
Android的Audio系统的相关类在android.media 包中，Java部分的代码路径为：
```  
frameworks/base/media/java/android/media
```
Audio系统主要实现了以下几个类：android.media.AudioSystem、android.media. Audio Track、android.media.AudioRecorder、android.media.AudioFormat。前面的3个类和本地代码是对应的，AudioFormat提供了一些Audio相关类型的枚举值。
注意：在Audio系统的Java代码中，虽然可以通过AudioTrack和AudioRecorder的write()和read()接口，在Java层对Audio的数据流进行操作。但是，更多的时候并不需要这样做，而是在本地代码中直接调用接口进行数据流的输入/输出，而Java层只进行控制类操作，不处理数据流。