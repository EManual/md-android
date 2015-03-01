Application是一个完整的应用，比如某个apk，它对应一个Application，它里面可能包含n个Activity。
涉及到的类
```  
froyo/frameworks/base/core/java/android/app/ApplicationContext.java
froyo/frameworks/base/core/java/android/app/ActivityThread.java
froyo/frameworks/base/services/java/com/android/server/am/ActivityManagerService.java
```
当我们启动手机之后，如果需要启动一个activity,ActivityThread,ActivityManagerService就开始发挥作用了,这里不做细述。
当我们真正的启动一个activity的时候，我们会把当前Application的ApplicationContext传进去，ApplicationContext实例持有一个mContextResolver对象，该对象对应于ApplicationContext的内部类ApplicationContentResolver。
当activity调用getContentResolver()时，我们实际调用的是当前ApplicationContext中的mContextResolver.
我们来看看效果图：

![img](http://emanual.github.io/md-android/img/component_provider/10_provider.jpg) 

由于黑色的继承关系，我们可以得到红色的调用关系我们看看效果图就知道了，它们两个是怎么样跳过文件来直接联系在一起的。这样的话我们就省去了很多环节，也给我们节省了时间这样的话我们也不容易出错，这样以来我们就会大大的提高了我们的工作效率，
代码片段如下：
Activity调用ContextWrapper的方法
```  
getContentResolver() {
	mBase.getContentResolver();
}
```
然后会调用到ApplicationContext的方法
```  
getContentResolver() {
	return mContentResolver;
}
```
Java代码：
```  
mContentResolve r = new ApplicationContentResolver(this, mainThread);
private static final class ApplicationContentResolver extends
		ContentResolver {
	public ApplicationContentResolver(Context context,
			ActivityThread mainThread) {
		super(context);
		mMainThread = mainThread;
	}
	@Override
	protected IContentProvider acquireProvider(Context context, String name) {
		return mMainThread.acquireProvider(context, name);
	}
	@Override
	public boolean releaseProvider(IContentProvider provider) {
		return mMainThread.releaseProvider(provider);
	}
	private final ActivityThread mMainThread;
}
```
当执行mContentResolver.query()的时候，我们会调用父类ContentResolver的query();
```  
public final Cursor query(Uri uri, String[] projection, String selection,
		String[] selectionArgs, String sortOrder) {
	IContentProvider provider = acquireProvider(uri);
	if (provider == null) {
		return null;
	}
	try {
		Cursor qCursor = provider.query(uri, projection, selection,
				selectionArgs, sortOrder);
		if (qCursor == null) {
			releaseProvider(provider);
			return null;
		}
		// CursorWrapperInner光标对象
		return new CursorWrapperInner(qCursor, provider);
	} catch (RemoteException e) {
		releaseProvider(provider);
		return null;
	} catch (RuntimeException e) {
		releaseProvider(provider);
		throw e;
	}
}
public final IContentProvider acquireProvider(Uri uri) {
	if (!SCHEME_CONTENT.equals(uri.getScheme())) {
		return null;
	}
	String auth = uri.getAuthority();
	if (auth != null) {
		return acquireProvider(mContext, uri.getAuthority());
	}
	return null;
}
```
此时，会调用子类实例aquireProvider(Context,name);
```  
mMainThread.acquireProvider (context, name);
```
Java代码：
```  
public final IContentProvider acquireProvider(Context c, String name) {
	IContentProvider provider = getProvider(c, name);
	if (provider == null)
		return null;
	IBinder jBinder = provider.asBinder(); // 获得binder对象，跨进程传递数据。
	synchronized (mProviderMap) {
		ProviderRefCount prc = mProviderRefCountMap.get(jBinder);
		if (prc == null) {
			mProviderRefCountMap.put(jBinder, new ProviderRefCount(1));
		} else {
			prc.count++;
		} // end else
	} // end synchronized
	return provider;
}
private final IContentProvider getProvider(Context context, String name) {
	synchronized (mProviderMap) {
		final ProviderRecord pr = mProviderMap.get(name); // ActivityThread中持有所有的Provider的实例
		if (pr != null) {
			return pr.mProvider;
		}
	}
	// 如果确实没有，则查找，并install，再没有就直接抛异常了。
	IActivityManager.ContentProviderHolder holder = null;
	try {
		holder = ActivityManagerNative.getDefault().getContentProvider(
				getApplicationThread(), name);
	} catch (RemoteException ex) {
	}
	if (holder == null) {
		Log.e(TAG, "Failed to find provider info for " + name);
		return null;
	}
	if (holder.permissionFailure != null) {
		throw new SecurityException("Permission 
				+ holder.permissionFailure + " required for provider
				+ name);
	}
	IContentProvider prov = installProvider(context, holder.provider,
			holder.info, true);
	// Log.i(TAG, "noReleaseNeeded=" + holder.noReleaseNeeded);
	if (holder.noReleaseNeeded || holder.provider == null) {
		// We are not going to release the provider if it is an external
		// 供应商,不在乎被释放,或如果它是
		// 本地提供运行这一过程。
		// Log.i(TAG, "*** NO RELEASE NEEDED");
		synchronized (mProviderMap) {
			mProviderRefCountMap.put(prov.asBinder(), new ProviderRefCount(
					10000));
		}
	}
	return prov;
}
```
上面的代码if(provider==null)后面有一个返回值，这个返回值一定要是空，不要写别的，后面的判断我们也都判断为空，这样我们就完成了contentResolver与contentProvider联系在一起了。