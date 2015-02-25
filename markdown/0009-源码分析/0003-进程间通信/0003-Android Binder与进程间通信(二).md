#### 真正的Binder
我们在上面所提到的这些Binder实际上只是JVM中的Binder，主要作用是提供了访问C++中的代理Binder，叫做BpBinder(BproxyBinder)。真正的Binder是Linux上的一个驱动设备，专门用来做android的数据交换。
![img](P)  
从上面分析可以看出，一次IPC通信大概有以下三个步骤：
1.在JVM中对数据进行序列化，并通过BinderProxy传递到C++中。
2.C++中的BpBinder对数据进行处理，并传入到Binder设备中(这里是在ProcessState类中处理并调用BpBinder)。
3.Service从内核设备中读取数据。
既然在C++中，处理数据主要是在ProcessState中，那么我们就来看看ProcessState的代码，在getContextObject中调用了getStrongProxyForHandle方法，从而获取了代理对象BpBinder：
```  
sp<IBinder> ProcessState::getStrongProxyForHandle(int32_t handle) 
{ 
	sp<IBinder> result;
	AutoMutex _l(mLock);
	handle_entry* e = lookupHandleLocked(handle);
	if (e != NULL) { 
		// We need to create a new BpBinder if there isn't currently one, OR we 
		// are unable to acquire a weak reference on this current one. See comment 
		// in getWeakProxyForHandle() for more info about this. 
		IBinder* b = e->binder;
		if (b == NULL || !e->refs->attemptIncWeak(this)) { 
			b = new BpBinder(handle);
			e->binder = b;
			if (b) e->refs = b->getWeakRefs(); 
				result = b;
		} else { 
			// This little bit of nastyness is to allow us to add a primary 
			// reference to the remote proxy when this team doesn't have one 
			// but another team is sending the handle to us. 
			result.force_set(b);
			e->refs->decWeak(this);
		} 
	} 
	return result; 
}
```
再来看看BpBinder中的transact方法代码：
```  
status_t BpBinder::transact(uint32_t code, const Parcel& data, Parcel* reply, uint32_t flags) 
{
	// Once a binder has died, it will never come back to life. 
	if (mAlive) {
		status_t status = IPCThreadState::self()->transact(
		mHandle, code, data, reply, flags);
		if (status == DEAD_OBJECT) mAlive = 0; 
		return status; 
	} 
	return DEAD_OBJECT; 
}	
```
在BpBinder中的transact函数中，只是调用了IPCThreadState::self()->transact方法，也就是说，数据处理是在IPCThreadState类中的transact。在transact中，它把请求的数据经过Binder设备发送给了Service。Service处理完请求后，又将结果原路返回给客户端。
![img](P)  