Binder机制是android中实现的进程间通信的架构，它采用的是c/s架构，client通过代理完成对server的调用。
ServiceManager
既然这里提到了server，那么我们有必要先了解下在android中是怎么来管理server的。先来看一个重要的Native进程：ServiceManager，从名字可以看出来，这个是用来管理所有server的。在init进程启动之后，会启动另外两个重要的进程，一个是我们上一篇讲的Zygote进程，另外一个就是这个ServiceManager进程了，这两个进程启动之后就建立了android的运行环境和server的管理环境。ServiceManager进程启动之后其他server就可以通过ServiceManager的add_service和check_service来添加和获取特定的server了。关于ServiceManager在接下来会详细介绍，因为Binder会涉及到ServiceManager，所以先简单介绍下，有个大概印象，知道他是干什么的就行了。
![img](P)  
Binder与进程间通信
我们所指的客户端没有特别说明的话就指应用程序。应为service和serviceManager通信也会涉及到IPC。
我们还是从activity的启动开始来研究Binder的机制。来看下startActivity涉及通信的类图：
![img](P)  
在ActivityManagerProxy中，有这句代码
```  
IBinder b = ServiceManager.getService("activity"); 
```
继续看下getService方法，在getService中对数据进行了序列化封装，并通过BinderProxy的native方法向ServiceManager发送请求，获取Binder的代理对象。看下getService代码： 	
```  
public class Snippet {
	/*
	 [Tags]* 从ServiceManager中获取service对应的代理Binder
	 [Tags]* @param na
	 [Tags]* @return
	 [Tags]* @throws RemoteException
	 [Tags]*/
	public IBinder getService(String name) throws RemoteException {
		Parcel data = Parcel.obtain();
		Parcel reply = Parcel.obtain();
		data.writeInterfaceToken(IServiceManager.descriptor);
		data.writeString(name);
		mRemote.transact(GET_SERVICE_TRANSACTION, data, reply, 0);
		IBinder binder = reply.readStrongBinder();
		reply.recycle();
		data.recycle();
		return binder;
	}
}
```
也就是说，在android中进行IPC的话，需要先通过ServiceManager获得客户端的代理，然后再通过该代理与对应的service进行通信。
1.建立和ServiceManager的连接，获取客户端对象的代理Binder。
2.客户端再通过该代理binder和服务器端进行通信。