#### Intents and Intent Filters
三种应用程序基本组件——activity，service和broadcast receiver——是使用称为intent的消息来激活的。Intent消息传递是一种组件间运行时绑定的机制。intent是Intent对象，它包含了需要做的操作的描述，或者，对于广播来说，包含了正在通知的消息内容。对于向这三种组件发送intent有不同的机制:
使用Context.startActivity()或Activity.startActivityForResult()，传入一个intent来启动一个activity。使用Activity.setResult()，传入一个intent来从activity中返回结果。
将intent对象传给Context.startService()来启动一个service或者传消息给一个运行的service。
将intent对象传给Context.bindService()来绑定一个service。
将intent对象传给Context.sendBroadcast()，Context.sendOrderedBroadcast()，或者Context.sendStickyBroadcast()等广播方法，则它们被传给 broadcast receiver。
在上述三种情况下，android系统会自己找到合适的activity，service，或者broadcastreceivers来响应intent。三者的intent相互独立互不干扰。
#### Intent Objects Intent对象
一个intent对象包含了接受该intent的组件的信息(例如需要的动作和该动作需要的数据)和android系统所需要的信息. 具体的说:
#### 组件名称
为一个ComponentName对象。它是目标组件的完整名(例如"com.example.project.app.FreneticActivity")和应用程序manifest文件设定的包名(例如"com.example.project")的组合。前者的包名部分和后者不一定一样。
组件名称是可选的. 如果设定了的话, Intent对象会被传给指定的类的一个实例。如果不设定，则android使用其它信息来定位合适的目标。
组件名称是使用setComponent()，setClass()，或setClassName()来设定，使用getComponent()来获取。
#### Action
一个字符串, 为请求的动作命名，或者，对于broadcast intent，发生的并且正在被报告的动作。例如:
```  
常量	目标组件	动作
ACTION_CALL	activity	发起一个电话呼叫。
ACTION_EDIT	activity	显示数据给用户来编辑。
ACTION_MAIN	activity	将该activity作为一个task的第一个activity启动,不传入参数也不期望返回值。
ACTION_SYNC	activity	将设备上的数据和一个服务器同步。
ACTION_BATTERY_LOW	broadcast receiver	发出电量不足的警告。
ACTION_HEADSET_PLUG	broadcast receiver	一个耳机正被插入或者拔出。
ACTION_SCREEN_ON	broadcast receiver	屏幕被点亮。
ACTION_TIMEZONE_CHANGED	broadcast receiver	时区设置改变。
```
你也可以定义自己的action字符串用来启动你的应用程序. 自定义的action应该包含应用程序的包名。例如"com.example.project.SHOW_COLOR"。
action很大程度上决定了intent的另外部分的结构，就像一个方法名决定了它接受的参数和返回值一样。因此，最好给action一个最能反映其作用的名字。
一个intent对象中的action是使用getAction()和setAction()来读写的。