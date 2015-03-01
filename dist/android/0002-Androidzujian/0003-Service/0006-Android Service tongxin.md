如果我们想保持和Service的通信，又不想让Service随着Activity退出而退出呢？你可以先startService()然后再bindService()。当你不需要绑定的时候就执行unbindService()方法，执行这个方法只会触发Service的onUnbind()而不会把这个Service销毁.这样就可以既保持和Service的通信，也不会随着Activity销毁而销毁了。
#### 提高 Service 优先级
Android系统对于内存管理有自己的一套方法，为了保障系统有序稳定的运信，系统内部会自动分配，控制程序的内存使用。当系统觉得当前的资源非常有限的时候，为了保证一些优先级高的程序能运行，就会杀掉一些他认为不重要的程序或者服务来释放内存。这样就能保证真正对用户有用的程序仍然再运行。如果你的Service碰上了这种情况，多半会先被杀掉。但如果你增加Service的优先级就能让他多留一会，我们可以用setForeground（true）来设置 Service 的优先级。
为什么是foreground？默认启动的Service是被标记为background，当前运行的Activity一般被标记为foreground，也就是说你给Service设置了foreground那么他就和正在运行的Activity类似优先级得到了一定的提高。当让这并不能保证你得Service永远不被杀掉，只是提高了他的优先级。
有一个方法可以给你更清晰的演示，进入$SDK/tools运行命令
```  
de> adb shell dumpsys activity|grep oom_adj 
Running Norm Proc  6: oom_adj= 0 ProcessRecord{43635cf0 12689:com.roiding.netraffic/10028} 
Running Norm Proc  5: oom_adj= 7 ProcessRecord{436feda0 12729:com.android.browser/10006} 
Running Norm Proc  4: oom_adj= 8 ProcessRecord{4367e838 12761:android.process.acore/10016} 
Running Norm Proc  3: oom_adj= 8 ProcessRecord{43691cd8 12754:com.google.process.gapps/10000} 
Running PERS Proc  1: oom_adj=-12 ProcessRecord{43506750 5941:com.android.phone/1001} 
Running PERS Proc  0: oom_adj=-100 ProcessRecord{4348fde0 5908:system/1000} de>
```
返回的一大堆东西，观察oom_adj的值，如果是大于8，一般就是属于backgroud，随时可能被干掉，数值越小证明优先级越高，被干掉的时间越晚。你看phone的程序是-12说明电话就是电话，其他什么都干了了，也的能接电话对吧。另外还有一个-100的，更邪乎因为是system如果他也完蛋了，你得系统也就挂了。
#### 用其他方式启动Service
其实不光能从Activity中启动Service，还有一个很有用的方法是接收系统的广播，这就要用到Receiver。在Mainfest文件中配置你得Receiver能接收什么样的广播消息，那么即使你得程序没有显示给用户，你的Service也能启动。你要做的就是继承android.content.BroadcastReceiver ，然后实现onReceive(Context context, Intent intent)方法，就可以启动你得Service了。这里不能 bindService 因为一个Receiver是一个短暂存在的对象，所以bind是没有什么意义的。