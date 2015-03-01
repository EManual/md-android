#### 为什么要关闭组件?
在用到组件时，有时候我们可能暂时性的不使用组件，但又不想把组件kill 掉，比如创建了一个broadcastReceiver广播监听器，用来想监听第一次开机启动后获得系统的许多相关信息，并保存在文件中，这样以后每次开机启动就不需要再去启动该服务了，也就是说如果没有把receiver关闭掉，就算是不做数据处理，但程序却还一直在后台运行会消耗电量和内存，这时候就需要把这个receiver给关闭掉。
#### 如何关闭组件?
关闭组件其实并不难，只要创建packageManager对象和ComponentName对象，并调用packageManager对象的setComponentEnabledSetting方法。方法描述如下：
```  
setComponentEnabledSetting(ComponentName componentName, int newState, int flags)
```
componentName：组件名称
newState：组件新的状态，可以设置三个值，分别是如下：
```  
不可用状态：COMPONENT_ENABLED_STATE_DISABLED
可用状态：COMPONENT_ENABLED_STATE_ENABLED
默认状态：COMPONENT_ENABLED_STATE_DEFAULT
flags:行为标签，值可以是DONT_KILL_APP或者0。
```
上个小例子。manifest文件中的配置：
```  
<receiver android:name=".ToggleReceiver">
<intent-filter>
	<action android:name="android.intent.action.BOOT_COMPLETED"/>
</intent-filter>
```
在对应的Receiver中的处理：
```  
final ComponentName receiver = new ComponentName(context,ToggleReceiver.class);
final PackageManager pm = context.getPackageManager();
count++;
if (count > 1) {
	pm.setComponentEnabledSetting(receiver,
	PackageManager.COMPONENT_ENABLED_STATE_DISABLED,
	PackageManager.DONT_KILL_APP);
}
```