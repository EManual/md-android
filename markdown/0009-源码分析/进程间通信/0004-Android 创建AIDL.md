#### 远程过程调用      
Android拥有轻量级的远程调用机制(RPC)—方法在本地调用，在远程执行(在其它进程中)，结果返回给调用者。这意味着将方法调用及其附带的数据分解为操作系统可以理解的形式，将其由本地进程和地址空间传送到远程进程和地址空间中，在远程重新装配并执行该调用。返回值沿着相反的方向传递。Android提供了实现该机制的所有代码，因此你只需要关注于如何定义和实现该RPC接口本身。
RPC接口只能包含方法。所有的方法都是同步执行的(本地方法被阻断，直到远程方法结束)，即使没有返回值。
简而言之，该机制的流程如下：你由使用简单的IDL(接口定义语言)定义要实现的RPC接口。根据接口的定义，aidl部类中包含管理你用IDL生成的远程过程调用需要的所有代码。两个内部类都实现了IBinder接口。其中一个在本地由系统内部使用，写代码时可以忽略它。另一个叫做Stub，扩展自Binder 类。作为对执行IPC调用的内部代码补充，它包含你在RPC接口中声明的方法。象图中说明的那样，你应该继承Stub来实现这些方法。
一般远程过程由服务来管理(因为服务可以通知系统关于进程和它连接的其它进程的信息)。它既有aidl。服务的客户端只有由aidl生成的接口文件。
接下来是服务和其客户端是如何建立连接的：
服务的客户端(为位于本地)应该实现onServiceConnected()和onServiceDisconnected()方法，这样它们就可以在成功与远程服务建立或断开连接后收到消息。它们应该调用bindService()来设置连接。
#### 服务的onBind()
方法应该被实现用作根据收到的意图(传入bindService() 的意图)，决定接受或拒绝连接。 
如果连接被接受，它返回一个Stub的子类。如果服务接受了连接，Android调用客户端的onServiceConnected()方法并传入一个IBinder对象，由服务管理的Stub子类的代理。通过该代理，客户端可以调用远程服务。 
上述简单的描述忽略了一些RPC机制的细节。更多信息参见用AIDL设计远程接口 和IBinder 类的描述。
在Android中，每个应用程序都可以有自己的进程。在写UI应用的时候，经常要用到Service。在不同的进程中，怎样传递对象呢?显然，Java中不允许跨进程内存共享。因此传递对象，只能把对象拆分成操作系统能理解的简单形式，以达到跨界对象访问的目的。在J2EE中，采用RMI的方式，可以通过序列化传递对象。在Android中，则采用AIDL的方式。理论上AIDL可以传递Bundle，实际上做起来却比较麻烦。
AIDL(AndRoid接口描述语言)是一种借口描述语言; 编译器可以通过aidl文件生成一段代码，通过预先定义的接口达到两个进程内部通信进程的目的。如果需要在一个Activity中，访问另一个Service中的某个对象，需要先将对象转化成AIDL可识别的参数(可能是多个参数)，然后使用AIDL来传递这些参数，在消息的接收端，使用这些参数组装成自己需要的对象。
AIDL的IPC的机制和COM或CORBA类似，是基于接口的，但它是轻量级的。它使用代理类在客户端和实现层间传递值。如果要使用AIDL，需要完成2件事情: 
1. 引入AIDL的相关类。2. 调用aidl产生的class。
具体实现步骤如下:
1、创建AIDL文件, 在这个文件里面定义接口, 该接口定义了可供客户端访问的方法和属性。 
如:
```  
import com.cmcc.demo.ITaskCallback;
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
注意: 理论上, 参数可以传递基本数据类型和String，还有就是Bundle的派生类，不过在Eclipse中，目前的ADT不支持Bundle做为参数，据说用Ant编译可以，我没做尝试。
2、编译AIDL文件，用Ant的话，可能需要手动，使用Eclipse plugin的话,可以根据adil文件自动生产java文件并编译，不需要人为介入。
3、在Java文件中，实现AIDL中定义的接口。编译器会根据AIDL接口，产生一个JAVA接口。这个接口有一个名为Stub的内部抽象类，它继承扩展了接口并实现了远程调用需要的几个方法。接下来就需要自己去实现自定义的几个接口了。
ITaskBinder.aidl中接口的实现，在MyService.java中接口以内嵌类的方式实现:
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
4、向客户端提供接口ITaskBinder, 如果写的是service，扩展该Service并重载onBind ()方法来返回一个实现上述接口的类的实例。这个地方返回的mBinder,就是上面通过内嵌了定义的那个。
```  
public IBinder onBind(Intent t) {
	printf("service on bind");
	return mBinder;
}
```
在Activity中，可以通过Binder定义的接口，来进行远程调用。