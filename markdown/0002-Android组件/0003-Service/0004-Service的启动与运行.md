有了 Service 类我们如何启动他呢，有两种方法：
```  
Context.startService()
Context.bindService() 
```
1、在同一个应用任何地方调用startService()方法就能启动Service了，然后系统会回调Service类的onCreate()以及 onStart() 方法。这样启动的Service会一直运行在后台，直到 Context.stopService()或者selfStop()方法被调用。
另外如果一个 Service 已经被启动，其他代码再试图调用startService()方法，是不会执行onCreate()的，但会重新执行一次 onStart()。 
2、另外一种bindService()方法的意思是，把这个Service和调用Service的客户类绑起来，如果调用这个客户类被销毁，Service也会被销毁。用这个方法的一个好处是，bindService()方法执行后Service会回调上边提到的onBind()方发，你可以从这里返回一个实现了IBind接口的类，在客户端操作这个类就能和这个服务通信了，比如得到Service运行的状态或其他操作。如果Service还没有运行，使用这个方法启动Service就会onCreate()方法而不会调用onStart()。 
总结： 
1、startService()的目的是回调onStart()方法，onCreate()方法是在Service不存在的时候调用的，如果Service存在（例如之前调用了bindService，那么Service的onCreate方法已经调用了）那么startService()将跳过onCreate()方法。
2、bindService()目的是回调onBind()方法，它的作用是在Service和调用者之间建立一个桥梁，并不负责更多的工作（例如一个Service需要连接服务器的操作），一般使用bindService来绑定到一个现有的Service（即通过StartService启动的服务）。
由于Service 的onStart()方法只有在startService()启动Service的情况下才调用，故使用onStart()的时候要注意这点。
#### 与Service通信并且让它持续运行
如果我们想保持和Service的通信，又不想让Service随着Activity退出而退出呢？你可以先startService()然后再bindService() 。当你不需要绑定的时候就执行unbindService()方法，执行这个方法只会触发Service的onUnbind()而不会把这个Service销毁。这样就可以既保持和Service的通信，也不会随着Activity销毁而销毁了。
#### 提高Service优先级 
Android系统对于内存管理有自己的一套方法，为了保障系统有序稳定的运信，系统内部会自动分配，控制程序的内存使用。当系统觉得当前的资源非常有限的时候，为了保证一些优先级高的程序能运行，就会杀掉一些他认为不重要的程序或者服务来释放内存。这样就能保证真正对用户有用的程序仍然再运行。如果你的Service碰上了这种情况，多半会先被杀掉。但如果你增加Service的优先级就能让他多留一会，我们可以用setForeground(true)来设置Service的优先级。
#### 为什么是foreground?
默认启动的Service是被标记为background，当前运行的Activity一般被标记为foreground，也就是说你给Service设置了foreground那么他就和正在运行的Activity类似优先级得到了一定的提高。当让这并不能保证你得Service永远不被杀掉，只是提高了他的优先级。