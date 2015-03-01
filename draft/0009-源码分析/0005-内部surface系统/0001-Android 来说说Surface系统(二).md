如果你有兴趣的话，看看Surface其他构造函数，最终都会调用native的实现，而这些native的实现将和SurfaceFlinger建立关系，但我们这里ViewRoot中的mSurface显然还没有到这一步。那它到底是怎么和SurfaceFlinger搞上的呢?这一切待会就会水落石出的。
另外，为什么ViewRoot是主人公呢?因为ViewRoot建立了客户端和SystemServer的关系。我们看看它的构造函数。
```  
public ViewRoot(Context context) { 
	super(); 
	getWindowSession(context.getMainLooper());
}
```
getWindowsession将建立和WindowManagerService的关系。
```  
public static IWindowSession getWindowSession(Looper mainLooper) { 
	synchronized (mStaticInit) { 
	if (!mInitialized) { 
		try { 
			//sWindowSession是通过Binder机制创建的。终于让我们看到点希望了 
			InputMethodManager imm = InputMethodManager.getInstance(mainLooper);
			sWindowSession = IWindowManager.Stub.asInterface( ServiceManager.getService("window")) .openSession(imm.getClient(), imm.getInputContext());
			mInitialized = true;
		} catch (RemoteException e) { 
		} 
	} 
	return sWindowSession; 
	} 
}	
```
上面跨Binder的进程调用另一端是WindowManagerService，代码在framework/base/services/java/com/android/server/WindowManagerService.java中。我们先不说这个。回过头来看看ViewRoot接下来的调用。
```  	
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView) { 
	synchronized (this) { 
		requestLayout();
		try { 
			res = sWindowSession.add(mWindow, mWindowAttributes,
			getHostVisibility(), mAttachInfo.mContentInsets);
		} 
	} 
}
```
requestLayout实现很简单，就是往handler中发送了一个消息。
```  	
public void requestLayout() { 
	checkThread();
	mLayoutRequested = true;
	scheduleTraversals(); //发送DO_TRAVERSAL消息
} 
public void scheduleTraversals() { 
	if (!mTraversalScheduled) { 
		mTraversalScheduled = true;
		sendEmptyMessage(DO_TRAVERSAL);
	} 
}
```
我们看看跨进程的那个调用。sWindowSession.add。它的最终实现在WindowManagerService中。
```  
public int add(IWindow window, WindowManager.LayoutParams attrs, 
	int viewVisibility, Rect outContentInsets) { 
	return addWindow(this, window, attrs, viewVisibility, outContentInsets); 
}
```
WindowSession是个内部类，会调用外部类的addWindow。这个函数巨复杂无比，但是我们的核心目标是找到创建显示相关的部分。所以，最后精简的话就简单了。
```  
public int addWindow(Session session, IWindow client, WindowManager.LayoutParams attrs, int viewVisibility, Rect outContentInsets) { 
	//创建一个WindowState，这个又是什么玩意儿呢？ 
	win = new WindowState(session, client, token,
	attachedWindow, attrs, viewVisibility);
	win.attach();
	return res; 
} 
```
WindowState类中有一个和Surface相关的成员变量，叫SurfaceSession。它会在attach函数中被创建。SurfaceSession嘛，就和SurfaceFlinger有关系了。我们待会看。我们知道ViewRoot创建及调用add后，我们客户端的View系统就和WindowManagerService建立了牢不可破的关系。
另外，我们知道ViewRoot是一个handler，而且刚才我们调用了requestLayout，所以接下来消息循环下一个将调用的就是ViewRoot的handleMessage。
```  
public void handleMessage(Message msg) { 
	switch (msg.what) { 
		case DO_TRAVERSAL: 
		performTraversals();
	}
}
```
performTraversals更加复杂无比，经过我仔细挑选，目标锁定为下面几个函数。当然，后面我们还会回到performTraversals，不过我们现在更感兴趣的是Surface是如何创建的。
```  
private void performTraversals() { 
	final View host = mView; 
	boolean initialized = false; 
	boolean contentInsetsChanged = false; 
	boolean visibleInsetsChanged; 
	try { 
		//ViewRoot也有一个Surface成员变量，叫mSurface，这个就是代表SurfaceFlinger的客户端 
		//ViewRoot在这个Surface上作画，最后将由SurfaceFlinger来合成显示。刚才说了mSurface还没有什么内容。 
		relayoutResult = relayoutWindow(params, viewVisibility, insetsPending);
		private int relayoutWindow(WindowManager.LayoutParams params, int viewVisibility, 
		boolean insetsPending) throws RemoteException { 
			//relayOut是跨进程调用，mSurface做为参数传进去了，看来离真相越来越近了呀！ 
			int relayoutResult = sWindowSession.relayout( 
			mWindow, params, (int) (mView.mMeasuredWidth * appScale + 0.5f), (int) (mView.mMeasuredHeight * appScale + 0.5f), viewVisibility, insetsPending, mWinFrame, mPendingContentInsets, mPendingVisibleInsets, mPendingConfiguration, mSurface);
			//mSurface做为参数传进去了。 
		} 
	}
}
```