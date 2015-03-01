对于Android的游戏音效播放，上次Android123已经告诉大家使用SoundPool类来实现，由于本次我们的游戏需要多种音效同时播放所以就选择了SoundPool类，它和Android提供常规的MediaPlayer类有哪些不同呢?
1.SoundPool载入音乐文件使用了独立的线程，不会阻塞UI主线程的操作。但是这里Android开发网提醒大家如果音效文件过大没有载入完成，我们调用play方法时可能产生严重的后果，这里Android SDK提供了一个SoundPool.OnLoadCompleteListener类来帮助我们了解媒体文件是否载入完成，我们重载 onLoadComplete(SoundPool soundPool, int sampleId, int status) 方法即可获得。
2.从上面的onLoadComplete方法可以看出该类有很多参数，比如类似id，是的SoundPool在load时可以处理多个媒体一次初始化并放入内存中，这里效率比MediaPlayer高了很多。
3.SoundPool类支持同时播放多个音效，这对于游戏来说是十分必要的，而MediaPlayer类是同步执行的只能一个文件一个文件的播放。
SoundPool类使用示例代码: 
```  
int load(Context context, int resId, int priority) //从APK资源载入
int load(FileDescriptor fd, long offset, long length, int priority) //从FileDescriptor对象载入
int load(AssetFileDescriptor afd, int priority) //从Asset对象载入
int load(String path, int priority) //从完整文件路径名载入
```
我们看到了每个load的重载版本的最后一个参数为优先级，这里用于播放多个文件时，系统会优先处理不过目前Android123提示大家SDK提到了目前并没有实现，所以没有实际的效果。
对于播放，可以使用 play(int soundID, float leftVolume, float rightVolume, int priority, int loop, float rate)  而停止则可以使用 pause(int streamID) 方法，这里的streamID和soundID均在构造SoundPool类的第一个参数中指明了总数量，而id从0开始。