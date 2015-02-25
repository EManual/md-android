Android中的两把锁 – WalkLock and KeyguardLock 详细分析。
WalkLock – 顾名思义 唤醒锁 点亮屏幕用的。
KeyguardLock – 顾名思义 键盘锁 解锁键盘用的。
详细介绍：
#### 1：WalkLock 唤醒锁
WalkLock真的能点亮屏幕吗？
答案是肯定的。可是有时候为什么不点亮屏幕，这个就是参数设置的问题了。
```  
PowerManager.newWakeLock(PowerManager.FULL_WAKE_LOCK | PowerManager.ACQUIRE_CAUSES_WAKEUP, "Gank");
PowerManager.FULL_WAKE_LOCK //这个参数是手机点亮的程度，（什么Cpu，屏幕亮度，键盘灯）
PowerManager.ACQUIRE_CAUSES_WAKEUP //关键是这个参数的理解。 
```
WalkLock点亮屏幕并非真的去点亮了屏幕，你可以理解为，它通过Android组件（Activity）去点亮了屏幕。
假如一个通知想去点亮屏幕，问题来了，它能点亮吗？ 肯定不行。
不过拥有这个PowerManager.ACQUIRE_CAUSES_WAKEU参数，你就可以点亮屏幕了。它使WalkLock不再依赖组件就可以点亮屏幕了。
WalkLock如何获得屏幕的状态？
PowerManager.isScreenOn()方法；这个方法返回true: 屏幕是唤醒的返回false:屏幕是休眠的。
WalkLock唤醒和休眠的方法？
WalkLock.aquire() 在屏幕休眠的状态下唤醒屏幕。
WalkLock.release() 在屏幕点亮的状态下，使屏幕休眠。
WalkLock.release()这个方法有个需要注意的地方。例如：WalkLockA对象先唤醒了屏幕再使屏幕休眠。
屏幕本身就是唤醒状态，WalkLockA对象没有唤醒过屏幕，WalkLockA对象如果尝试使屏幕休眠。会产生一个异常 UnLock Sreen。
#### 2:KeyguardLock 键盘锁
KeyguardLock获得当前屏幕是否解锁？
KeygroundManager.inKeyguardRestrictedInputMode() 返回true表示键盘锁住， 返回false表示键盘解锁中。
KeyguardLock给屏幕解锁和上锁。
KeyguardLock.disableKeyguard()解锁键盘。
KeyguardLock.reenableKeyguard()锁键盘。
KeyguardLock没有上面唤醒锁的问题，就是无论你键盘是否由KeyguardLockA解锁，你调用KeyguardLockA对象的reenableKeyguard（）方法都不会有异常。
这两把锁一些概念性的理解，假如你认为你获得了一个键盘锁对象，你就可以锁屏幕了。这个就大错特错了。
你锁不了其他程序打开的屏幕（如果可以的话，一个for循环一直锁你屏幕，你哭都没眼泪）。
你可以控制自己的锁，别想着别人的锁。
#### 最后总结下用法：
一般这两把锁都是配合使用的，你解锁屏幕的时候肯定不希望屏幕漆黑一片。关闭键盘锁的时候希望屏幕也同时休眠。
#### 问题：
1：我尝试手动关闭屏幕，可是总继续亮那么一小会。
2：如果手机自动关闭屏幕的话，不会有这个问题。
主要代码展示：
```  
KeyguardManager keyguardManager = (KeyguardManager) this
		.getSystemService(Context.KEYGUARD_SERVICE);
KeyguardLock keyguardLock = keyguardManager.newKeyguardLock("随便写点啥都行");
keyguardLock.disableKeyguard();
[Tags]/**
 [Tags]* 点亮屏幕
 [Tags]*/
private void lightScreen() {
	PowerManager powerManager = (PowerManager) this
			.getSystemService(Context.POWER_SERVICE);
	WakeLock wakeLock = powerManager.newWakeLock(
			PowerManager.FULL_WAKE_LOCK
					| PowerManager.ACQUIRE_CAUSES_WAKEUP, "");
	wakeLock.acquire();
}
```