这些都是manifest.XML里的代码。每一段代码都有它的意思，这些资料主要就是让新手们知道每一段代码都是干什么的。什么地方出错了，先看看manifest.xml里有没有缺少权限。
```  
android:allowTaskReparenting
```
这句话的意思就是（是否允许activity更换从属的任务，比如从短信息任务 切换到浏览器任务。）
```  
android:alwaysRetainTaskState
```
是否保留状态不变， 比如切换回home, 再从新打开， activity处于最后的状态
```  
android:clearTaskOnLanunch
```
这句话的意思就是（比方说 A 是 activity, eoe 是被A 触发的 activity, 然后返回 Home, 从新启动A，是否显示 eoe)
```  
android:configChanges
```
在我们配置list发生修改时， 是否调用 onConfigurationChanged() 方法 
```  
android:enabled
```
activity 是否可以被实例化。 上面的这句话是必须有的。而且和重要。
```  
android:excludeFromRecents
```
这据主要的意思就就（是否可被显示在最近打开的activity列表里）
```  
android:exported
```
是否允许activity被其它程序调用
```  
android:finishOnTaskLaunch
```
是否关闭已打开的activity当用户重新启动这个任务的时候。我感觉就是你个提醒的意思。
```  
android:launchMode
```
activity启动方式， （"standard" "singleTop"）（ "singleTask" "singleInstance"）其中前两个为一组， 后两个为一组 
```  
android:multiprocess
```
这句话是非常主要的，因为没有一个应用是用单线程来完成的，这句话的意思是（允许多进程）
```  
android:name
```
这个是必须有的，意思是activity的类名， 必须指定
```  
android:onHistory
```
是否需要移除这个activity当用户切换到其他屏幕时。 这个属性是 API level 3 中引入的
```  
android:process
```
一个activity运行时所在的进程名，所有程序组件运行在应用程序默认的进程中，这个进程名跟应用程序的包名一致。中的元素process属性能够为所有组件设定一个新的默认值。但是任何组件都可以覆盖这个默认值，允许你将你的程序放在多进程中运行。 
如果这个属性被分配的名字以:开头，当这个activity运行时,一个新的专属于这个程序的进程将会被创建。如果这个进程名以小写字母开头，这个activity将会运行在全局的进程中，被它的许可所提供。