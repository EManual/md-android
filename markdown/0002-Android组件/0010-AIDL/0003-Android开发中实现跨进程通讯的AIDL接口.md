在Android开发中，每个应用程序都可以有自己的进程。在写UI应用的时候，经常要用到Service。在不同的进程中，怎样传递对象呢? 
显然，Java中不允许跨进程内存共享。因此传递对象，只能把对象拆分成操作系统能理解的简单形式，以达到跨界对象访问的目的。在J2EE中，采用RMI的方式，可以通过序列化传递对象。在Android中，则采用AIDL的方式。理论上AIDL可以传递Bundle，实际上做起来却比较麻烦。
AIDL(AndRoid接口描述语言)是一种接口描述语言，编译器可以通过aidl文件生成一段代码，通过预先定义的接口达到两个进程内部通信进程的目的。如果需要在一个Activity中，访问另一个Service中的某个对象，需要先将对象转化成AIDL可识别的参数(可能是多个参数)，然后使用AIDL来传递这些参数， 在消息的接收端，使用这些参数组装成自己需要的对象。
AIDL的IPC的机制和COM或CORBA类似，是基于接口的，但它是轻量级的。它使用代理类在客户端和实现层间传递值。如果要使用AIDL，需要完成2件事情: 
```  
(1) 引入AIDL的相关类。
(2) 调用aidl产生的class。
```
Android开发中实现跨进程通讯的具体步骤如下:
1、创建AIDL文件, 在这个文件里面定义接口，该接口定义了可供客户端访问的方法和属性。 如: ITaskBinder.adil 
```  
interface ITaskBinder {
	boolean isTaskRunning();
	void stopRunningTask();
	void registerCallback(ITaskCallback cb);
	void unregisterCallback(ITaskCallback cb);
}
```
其中: ITaskCallback在文件ITaskCallback.aidl中定义:
```  
interface ITaskCallback {
	void actionPerformed(int actionId);
}
```
注意:理论上，参数可以传递基本数据类型和String，还有就是Bundle的派生类，不过在Eclipse中，目前的ADT不支持Bundle做为参数，据说用Ant编译可以，我没做尝试。
2、Android开发中编译AIDL文件，用Ant的话，可能需要手动，使用Eclipse plugin的话，可以根据adil文件自动生产java文件并编译，不需要人为介入。
3、在Java文件中，实现AIDL中定义的接口。
编译器会根据AIDL接口，产生一个JAVA接口。这个接口有一个名为Stub的内部抽象类，它继承扩展了接口并实现了远程调用需要的几个方法。接下来就需要自己去实现自定义的几个接口了。
ITaskBinder.aidl中接口的实现, 在MyService.java中接口以内嵌类的方式实现:
```  
private final ITaskBinder.Stub mBinder = new ITaskBinder.Stub() {
	public void stopRunningTask() {
	}
	public boolean isTaskRunning() {
		return false;
	}
	public void registerCallback(ITaskCallback cb) {
		if (cb != null)
			mCallbacks.register(cb);
	}
	public void unregisterCallback(ITaskCallback cb) {
		if (cb != null)
			mCallbacks.unregister(cb);
	}
};
```
在MyActivity.java中ITaskCallback.aidl接口实现:
```  
private ITaskCallback mCallback = new ITaskCallback.Stub() {
	public void actionPerformed(int id) {
		printf("callback id=" + id);
	}
};
```
4、向客户端提供接口ITaskBinder，如果写的是service，扩展该Service并重载onBind()方法来返回一个实现上述接口的类的实例。这个地方返回的mBinder，就是上面通过内嵌了定义的那个。(MyService.java):
```  
public IBinder onBind(Intent t) {
	printf("service on bind");
	return mBinder;
}
```
在Activity中，可以通过Binder定义的接口，来进行远程调用。
5、在服务器端回调客户端的函数。
前提是当客户端获取的IBinder接口的时候,要去注册回调函数，只有这样，服务器端才知道该调用那些函数在:MyService.java中:
```  
void callback(int val) {
	final int N = mCallbacks.beginBroadcast();
	for (int i = 0; i < N; i++) {
		try {
			mCallbacks.getBroadcastItem(i).actionPerformed(val);
		} catch (RemoteException e) {
			// The RemoteCallbackList will take care of removing
			// the dead object for us.
		}
		mCallbacks.finishBroadcast();
	}
}
```