我们大家在用手机的时候，会遇到这样的一个问题，就是想让我们的手机屏幕一直亮着怎么办。大家会想到的是，在手机设置里有一个不省电模式，选择这个就可以了，但是我们要在代码中是怎么样办那，我们。其实eoe有两种方法就可以解决这个问题，大家想一想，我们在android里那个地方老是常定义权限呀，有了这个提示，大家就会想到是哪个文件了吧，AndroidManifest.xml:我们要在这个文件里定义一下权限就可以实现我们的手机屏幕保持常亮了。这个方法也是最简单的一个方法，那么我们下面就来看看它的代码：
```  
<uses-permission android:name="android.permission.WAKE_LOCK" />  
```
代码如下：
```  
PowerManager pm = (PowerManager) getSystemService(Context.POWER_SERVICE); 
PowerManager.WakeLock mWakeLock = pm.newWakeLock(PowerManager.SCREEN_DIM_WAKE_LOCK, "My Tag"); 
// in onResume() call
mWakeLock.acquire(); 
// in onPause() call 
mWakeLock.release();
```
我们再在main代码中写上PowerManager.SCREEN_DIM_WAKE_LOCK，这个是我们android系统提供给我们的，我们要把它用上，这句代码的意思是长亮的意思，这样我们就可以实现了，因为我们在上面已经定义了权限。这样我们就有权利使用这个长亮属性。这就是第一种方法。
第二种我们不怎么常用，但有的时候我们也能用得到，我们就来讲讲这第二种方法吧。这种方法我们就在main代码中做一个方法，这个方法就是onCreate(Bundle icicle)我们要在括号里写上参数，这样我们才可以用这个参数，我们在super.onCreate(icicle);这个句的意思就是得到参数，我们也就是实现完了，最后我们在找到LayoutParams.FLAG_KEEP_SCREEN_ON这个android系统提供给我们的属性，这样我们第二个方法就完事了，这个方法就是不用在AndroidManifest.xml:里定义权限了。这样也不比较麻烦，但有时会把参数给忘了，这个是重点，因为这样我们也实现不了效果，这么说的，两个方法有利有弊，用的时候就要看开发者自己的喜好了，喜好哪个就用哪个。
```  
@Override 
protected void onCreate(Bundle icicle) { 
	super.onCreate(icicle);
	getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
}
```