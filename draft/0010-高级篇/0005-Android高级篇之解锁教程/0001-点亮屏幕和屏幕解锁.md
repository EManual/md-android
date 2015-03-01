下面的代码可以用来点亮屏幕。
```  
PowerManager pm = (PowerManager)getSystemService(POWER_SERVICE);
mWakelock = pm.newWakeLock(PowerManager.ACQUIRE_CAUSES_WAKEUP |PowerManager.SCREEN_DIM_WAKE_LOCK, "SimpleTimer");
mWakelock.acquire();
```
下面的代码用来屏幕解锁。
```  
KeyguardManager keyguardManager = (KeyguardManager)getSystemService(KEYGUARD_SERVICE);
KeyguardLock keyguardLock = keyguardManager.newKeyguardLock("");
keyguardLock.disableKeyguard();
```
使用这两段代码，需要在AndroidManifest文件中加入。
```  
<uses-permission android:name="android.permission.DISABLE_KEYGUARD"></uses-permission>
<uses-permission android:name="android.permission.WAKE_LOCK"></uses-permission>
```