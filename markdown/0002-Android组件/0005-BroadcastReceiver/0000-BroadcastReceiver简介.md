在 Android 中使用 Activity, Service, Broadcast, BroadcastReceiver 
```  
活动(Activity) - 用于表现功能  
服务(Service) - 相当于后台运行的 Activity 
广播(Broadcast) - 用于发送广播  
广播接收器(BroadcastReceiver) - 用于接收广播 
Intent - 用于连接以上各个组件，并在其间传递消息 
```
#### BroadcastReceiver 
在Android中，Broadcast是一种广泛运用的在应用程序之间传输信息的机制。而BroadcastReceiver是对发送出来的Broadcast进行过滤接受并响应的一类组件。下面将详细的阐述如何发送Broadcast和使用BroadcastReceiver过滤接收的过程： 
首先在需要发送信息的地方，把要发送的信息和用于过滤的信息(如Action、Category)装入一个Intent对象，然后通过调用Context.sendBroadcast()、sendOrderBroadcast()或sendStickyBroadcast()方法，把 Intent对象以广播方式发送出去。 
当Intent发送以后，所有已经注册的BroadcastReceiver会检查注册时的IntentFilter是否与发送的Intent相匹配，若匹配则就会调用BroadcastReceiver的onReceive()方法。所以当我们定义一个BroadcastReceiver的时候，都需要 实现onReceive()方法。 
#### 注册BroadcastReceiver有两种方式: 
一种方式是，静态的在AndroidManifest.xml中用<receiver>标签生命注册，并在标签内用<intent- filter>标签设置过滤器。 
另一种方式是，动态的在代码中先定义并设置好一个IntentFilter对象，然后在需要注册的地方调Context.registerReceiver()方法，如果取消时就调用Context.unregisterReceiver()方法。如果用动态方式注册的BroadcastReceiver的Context对象被销毁时，BroadcastReceiver也就自动取消注册了。 
另外，若在使用sendBroadcast()的方法是指定了接收权限，则只有在AndroidManifest.xml中用<uses-permission>标签声明了拥有此权限的BroascastReceiver才会有可能接收到发送来的Broadcast。 
同样，若在注册BroadcastReceiver时指定了可接收的Broadcast的权限，则只有在包内的AndroidManifest.xml中用<uses-permission>标签声明了，拥有此权限的Context对象所发送的Broadcast才能被这个 BroadcastReceiver所接收。 
#### 动态注册： 
```  
IntentFilter intentFilter = new IntentFilter(); 
intentFilter.addAction(String);--为 BroadcastReceiver指定action，使之用于接收同action的广播 registerReceiver(BroadcastReceiver,intentFilter); 
```
一般：在onStart中注册，onStop中取消unregisterReceiver
发送广播消息：extends Service 
指定广播目标Action：Intent Intent = new Intent(action-String) 
--指定了此action的receiver会接收此广播 
需传递参数(可选) putExtra(); 
发送：sendBroadcast(Intent);